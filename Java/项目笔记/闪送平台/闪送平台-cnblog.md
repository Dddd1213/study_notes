# 闪送平台

[TOC]

# 项目难点

## 1. 1 Redis+Lua脚本+自定义注解+过滤器实现IP限流

- （看懂了，但是只会照葫芦画瓢地写）
- 原理：将用户一段时间的访问次数记录下来，超过某个值拒绝访问

### Lua脚本

- **脚本怎么调用？**√

  - 脚本中调用redis：redis.call('命令名称', 'key', '其它参数', .....)
  - redis中执行脚本：EVAL
  - 在spirngboot项目中的使用见下部分IdAccessLimitInterceptor的写法

  ![image-20231117104111744](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125149151-380708428.png)

### IP限流的实现

1. lua脚本：获取当前用户的使用次数，如果这段时间使用次数大于最大次数，就不允许它访问

```lua
--获取KEY
local key1 = KEYS[1]

--val计数器，ttl剩余过期时间
local val = redis.call('incr', key1)
local ttl = redis.call('ttl', key1)

--获取ARGV内的参数并打印，过期时间(expire)，最大次数(times)
local expire = ARGV[1]
local times = ARGV[2]

--打印调试信息
redis.log(redis.LOG_DEBUG,tostring(times))
redis.log(redis.LOG_DEBUG,tostring(expire))

redis.log(redis.LOG_NOTICE, "incr "..key1.." "..val);
--如果自增后为1，说明第一次操作，直接把key1的过期时间设置为expire
if val == 1 then
    redis.call('expire', key1, tonumber(expire))
else
    -- 如果没有设置过期时间，也把key1的过期时间设为expire
    if ttl == -1 then
        redis.call('expire', key1, tonumber(expire))
    end
end
--如果自增后的值大于传递的最大次数times，则需要限流，返回0
if val > tonumber(times) then
    return 0
end
--否则不用，返回1
return 1
```

2. id限流拦截器： IdAccessLimitInterceptor implements HandlerInterceptor

   - **拦截器配置？**√ 在WebMvcConfig中

     ![image-20231117115218902](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125148648-1145103948.png)

```java
@Component
public class IdAccessLimitInterceptor implements HandlerInterceptor {
    public static final Logger log = LoggerFactory.getLogger(IdAccessLimitInterceptor.class);
    @Autowired
    StringRedisTemplate redisTemplate;

    private DefaultRedisScript<Long> getRedisScript;

    /**
     * 初始化lua脚本
     */
    @PostConstruct
    public void init() {
        getRedisScript = new DefaultRedisScript<>();
        getRedisScript.setResultType(Long.class);
        //这个脚本写好放在Resources的lua目录下
        getRedisScript.setScriptSource(new ResourceScriptSource(new ClassPathResource("lua/idAccessLimit.lua")));
        log.info("lua脚本初始化完成");
    }

    @Override
    public boolean preHandle(@NotNull HttpServletRequest request, @NotNull HttpServletResponse response, @NotNull Object handler) {
        try {
            if (handler instanceof HandlerMethod) {
                HandlerMethod handlerMethod = (HandlerMethod) handler;
                IdAccessLimit idAccessLimit = handlerMethod.getMethodAnnotation(IdAccessLimit.class);
                //如果没有注解,直接通行
                if (null == idAccessLimit) {
                    return true;
                }
                //获取注解上的参数
                int seconds = idAccessLimit.seconds();
                int maxCount = idAccessLimit.maxCount();
                String limitKey = idAccessLimit.key();
                Long id = JwtAuthenticationTokenFilter.threadLocal.get();

                //servletPath用于标识某一个请求
                String servletPath = request.getServletPath();
                String key = limitKey + "id:" + id + servletPath;
                // 已经访问的次数(不包括这次访问)
                String count = redisTemplate.opsForValue().get(key);
                log.info("(id限流请求次数) id:{} 接口名:{} 已访问次数:{}", id, servletPath, count == null ? 0 : count);
                //执行lua脚本
                ArrayList<String> keyList = new ArrayList<>();
                keyList.add(key);
                //调用脚本，这里的getRedisScript是在init的时候就把指定脚本放进去了的
                Long execute = redisTemplate.execute(getRedisScript, keyList, String.valueOf(seconds), String.valueOf(maxCount));
                if (execute != null && execute == 0) {
                    log.info("(id限流请求次数) id:{} 接口名:{} 超过访问次数:{}", id, servletPath, maxCount);
                    response(response);
                    return false;
                }
                return true;
            }
        } catch (Exception e) {
            log.error("(id限流请求次数) 请求异常 ex:{}", e.getMessage());
        }
        return true;
    }

    /**
     * 拦截器异常响应
     */
    public void response(HttpServletResponse response) throws IOException {
        response.setCharacterEncoding("UTF-8");
        response.setContentType(MediaType.APPLICATION_JSON_VALUE);
        ObjectMapper objectMapper = new ObjectMapper();
        // R.fail 是接口响应统一封装返回
        response.getWriter().println(objectMapper.writeValueAsString(Response.error(ErrorCode.REQUEST_FREQUENT)));
    }
}
```

3. 注解

   - **哪写了这个注解？**√ 就在更改订单状态那有

   ![image-20231117115340791](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125148021-1860510025.png)

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
public @interface IdAccessLimit {

    /**
     * 限流key
     */
    String key() default "limiter:";

    /**
     * 指定时间
     */
    int seconds() default 60;

    /**
     * 指定时间内的访问次数
     */
    int maxCount() default 30;
}
```

- 另外：也可用AOP写ip限流：[【精选】自定义注解+Aop 实现 SpringBoot 限流_使用自定义注解和aop对接口进行限流(访问、请求次数)-CSDN博客](https://blog.csdn.net/luolearn/article/details/120668323)



## 1.2 SecurityConfig如何配置，rbac模型了解一下ConfigProperties与配置文件配合，可以快速设置哪些接口需要鉴权

- @EnableGlobalMethodSecurity(prePostEnabled = true)
  - 里面设置true会解锁@PreAuthorize和@PostAuthorize两个注解 **（程序中好像没用上？√）**

- 配置：允许匿名访问的接口，需要鉴权的接口，异常处理器，跨域等
- rbac：
  - 用户表user：记录用户id，name
  - 权限表menu：记录每个操作需要的权限
  - 角色表role：一个角色对应一个权限组
  - 角色权限关联表role_menu：关联角色表和权限表，多对多
  - 用户角色关联表user_role：关联用户表和角色表，多对多

- ConfigProperties：将application-Permission中的配置转化成实体类，这样在SecurityConfig中配置需要鉴权和放行的接口时直接注入然后get，会方便很多

  <img src="C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231116170141183.png" alt="image-20231116170141183" style="zoom:40%;" />

  

## 1.3 JwtAuthenticationTokenFilter过滤器，无感验证token，并将信息写入Threadlocal

- 放在所有过滤器前面，如果token为空就直接放行到登录页（后面还有其他过滤器对非法请求进行拦截），不为空就解析并进行权限认证

- 继承了OncePerRequestFilter抽象方法，确保每次请求只执行一次过滤

  - 默认情况下，servlet2.3中filter会执行一切请求，包括forward和include那些内部转发

- 重写doFilterInternal方法

- Bearer：好像就是一种规范，类似的还有basic，digest等，格式就是bearer <token\>，解析token的时候把前面的bearer去掉

- 解析jwt，获取userId

- 由userId从redis中获取userInfo，从userInfo中解析出userPermission，判空抛异常

  - **啥时候存进去的？√**

    - UserServiceImpl中getLoginResponse里存的redis，这个getLoginResponse在俩登录接口中被调用

      ![image-20231116163812808](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125147685-571404572.png)

- 把userPermission存到UsernamePasswordAuthenticationToken再存入SecurityContextHolder，以在Security框架中进行后续校验

  - 这里第一个参数传userPermission，后俩为null
  - **第三个参数不存权限信息，怎么进行权限校验？**√ 看1.4
  - ~~这代码鉴权了吗 怎么找不到注解。。~~ 鉴了！看1.4

-  然后把userId存到threadLocal中，方便在其他地方直接通过threadLocal获取用户id

  <img src="C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231116161703490.png" alt="image-20231116161703490" style="zoom:40%;" />

- 放行

- 代码复现：

```java
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {
    @Autowired
    StringRedisTemplate stringRedisTemplate;

    public static ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        //获取token
        String accessToken = request.getHeader(KeyProperties.TOKEN_HEADER);
        if (StringUtils.isEmpty(accessToken)) {
            filterChain.doFilter(request, response);
            //加return为了返回时第二次经过过滤器不再执行下面的代码
            return;
        }
        if (accessToken.startsWith("Bearer ")) {
            accessToken = accessToken.substring(7);
        }
        //解析token
        String userId = "";
        Claims claims = null;
        try {
            claims = JwtUtil.parseJWT(accessToken);
        } catch (Exception e) {
            throw new BizException(ErrorCode.TOKEN_PARSE_ERROR);
        }
        userId = claims.getSubject();
        //从redis获取用户信息
        String key = KeyProperties.TOKEN_PREFIX + userId;
        String userInfo = stringRedisTemplate.opsForValue().get(key);
        UserPermission userPermission = JSONObject.parseObject(userInfo, UserPermission.class);
        if (userPermission == null) {
            throw new BizException(ErrorCode.TOKEN_PARSE_ERROR);
        }
        //存入SecurityContextHolder
        // 获取权限信息封装到authticationToken中
        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(userPermission, null, null);
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);
        //存入ThreadLocal
        threadLocal.set(Long.valueOf(userId));
        //放行
        filterChain.doFilter(request, response);
    }
}
```

### 比较之前三更的课

- 之前学的时候过滤器中UsernamePasswordAuthenticationToken第一个参数存的是LoginUser（继承了UserDetail类，封装了User，重写getAuthorities方法，把permission存入的是框架中的SimpleGrantedAuthority中），第三个参数存的权限信息，然后存到SecurityContextHolder中

- **为什么三更那个的LoginUser实现了UserDetails这个项目中没有？啥时候需要用UserDetails  √**

  - 三更的登录的时候是通过user和password传进去得到的authentiacate然后得到用户信息；最后一层查数据库implements了UserDetailsService，然后在这个地方Override了以UserDetails为返回值的loadUserByUsername（注：图2是userservice中的login方法内截出来的）

  ![image-20231116185855170](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125147104-865529807.png)

  ![image-20231116161238342](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125146706-1095690325.png)

  - 但是闪送平台是直接在userServiceImpl里面直接用mybatis查了

    ![image-20231116185627531](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125146272-846078504.png)

- **那些什么过滤器什么service如果不重写不自己实现的话它们还在那吗，为什么在/不在，如果还在为什么不会对程序产生影响？**√

  - 比如上面这个，用mybatis查完就直接return了，后面的就不会再继续执行



## 1.4 设置权限

- config.permission.expression中的RbacExpressionRoot.写了鉴权会用到的方法（hasAuthority和hasRole），且用Component指定了value为rbacExpressionRoot

![image-20231116194246468](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125145722-1823701747.png)

- 这个项目里设置权限不是用的注解，是在配置类里指定的

![image-20231116184450943](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125145202-1173108740.png)

- 如果是用Security框架中自带的的注解的话底层代码是这样的：
  - **这个时候就是一定得在filter里往第三个参数传authorities了？**√
  - 用demo试了一下好像是这样，不然会报没有权限

![image-20231116194219486](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231117125144452-1579665832.png)



# 疑点

- **为什么要把userPermission存入redis中？Jwt的使用的一大优势不就是为了不在服务器端存储数据吗？如果需要做集群怎么处理？**

- **filter和Interceptor的区别？都啥时候用？**

  - **执行顺序**：Filter 是 Servlet 容器接收到请求后，但是在调用 Servlet 被调用执行前执行的；而 Interceptor 是 Servlet 被调用后，在请求到达 Controller 前执行

  <img src="C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231117121936103.png" alt="image-20231117121936103" style="zoom:40%;" />

  <img src="C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231117122025896.png" alt="image-20231117122025896" style="zoom:33%;" />

  - **拦截粒度**：Filter 只能对 request、response 进行拦截；Interceptor 不仅可以对 request、response 进行操作，也可以对 handler、modelAndView 进行操作，具备了对 SpringMVC 组件的操作能力
  - **依赖从属**：Filter 依赖于 Servlet 容器；而 Interceptor 不依赖于 Servlet，依赖于 Spring 框架
  - **使用场景**：Filter常用于字符编码设置，相应数据压缩等通用功能；Interceptor常用于是否登录判断，权限判断，日志等业务功能。
  - 综上，在基于 SpringBoot 的项目开发中，如果有需要对请求拦截处理的场景，Filter 和 HandlerInterceptor两者之间，优先选择 HandlerInterceptor
  - （有点抽象。。）



## 小问题记录

- 没法看源码详细注释信息
  - mvn dependency:sources



# 优化

## 限流方式

- 项目中采用的是固定窗口限流，可能会存在一种临界问题：

![image-20231117223244716](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231118103149187-1099333291.png)

#### 优化：用redisson调用令牌桶限流

- 这里直接搬了自己项目的代码，基本上通用，在需要限流的地方调用doRateLimit方法即可，用aop啥的编进去也行

```java
@Configuration
@Data
@ConfigurationProperties(prefix = "spring.redis")
public class RedissonConfig {

    private Integer database;
    private String host;
    private Integer port;
    private String password;

    @Bean
    public RedissonClient getRedissonClient() throws IOException {
        // 1. Create config object
        Config config = new Config();
        config.useSingleServer()
                .setDatabase(database)
                .setPassword(password)
                .setAddress("redis://" + host + ":" + port);
        RedissonClient redisson = Redisson.create(config);
        return redisson;
    }
}
```

```java
@Service
public class RedisLimiterManager {
    @Resource
    private RedissonClient redissonClient;

    /**
     * key 用来区分不同的限流，如不同的id分别统计限流
     * @param key
     */
    public void doRateLimit(String key){
        RRateLimiter rateLimiter = redissonClient.getRateLimiter(key);
        //每秒允许访问两次
        rateLimiter.trySetRate(RateType.OVERALL,2,1, RateIntervalUnit.SECONDS);

        //令牌桶：每个操作取几个令牌
        //应用场景：比如会员可以设置更少的令牌，让它能更快处理；普通用户一个操作消耗更多令牌
        boolean acquire = rateLimiter.tryAcquire(1);
        
        if(!acquire){
            throw new BusinessException(ErrorCode.TOO_MANY_REQUEST);
        }
    }

}
```

