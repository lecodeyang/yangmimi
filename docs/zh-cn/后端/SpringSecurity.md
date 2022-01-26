## SpringSecurity

强调两点 认证与授权,WebSecurityConfig依赖于MyUserDatailService

在项目的启动的时候WebSecurityConfig,MyUserDatailService就会被实例化，随时可以使用

1.引入springSecurity模块

```xml
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
```



2.编写springSecurity配置类，配置角色，定制Http的安全规则，配置匹配路径

请求http，经过MyUserDatailService的configure(HttpSecurity http) 过滤中查看是否需要权限

```java
   @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers(HttpMethod.POST,"/add-user").permitAll()// 允许post请求/add-user访问
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("admin")
                .antMatchers("/level2/**").hasRole("user")
                .antMatchers("/level3/**").hasRole("admin")
               // .anyRequest().authenticated() // 所有请求都需要验证
                .and()
                .formLogin().loginPage("/userlogin").loginProcessingUrl("/authentication/form")// 使用默认的登录页面
                .and()
                .logout().logoutSuccessUrl("/")
                .and()
                .csrf().disable();// post请求要关闭csrf验证,不然访问报错
    }
```

3.需要权限就去验证，然后就让 configure(AuthenticationManagerBuilder auth)验证，要验证就要有两个对象，一个是用户输入的，一个是数据库中的，

```java
  @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //用户的输入与其对比，成功准许通过
        //  验证身份就是加载响应的userDetailServic，看看是否和用户输入的账号、密码、权限等信息匹配
        auth
                .userDetailsService(userDetailService)
                //认证规则的密码
                .passwordEncoder(passwordEncoder());
    }
```

4.userDetailService就是根据用户用户输入的用户名，找到之后返回一个对象,注意返回对象

```java
package com.example.demo.config;

import com.example.demo.Services.UserService;
import com.example.demo.entity.Userinfo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

@Service
public class MyUserDatailService implements UserDetailsService {
    @Autowired
    private UserService userService;
    //这里就是service层，负责调用dao层来查找用户
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        
            Userinfo user = userService.selectUserByUsername(username);
            //根据用户名取出数据库的这个对象
            if (user == null){
                System.out.print("用户不存在");
                throw new UsernameNotFoundException("该用户不存在");
            }
            //角色一定要是ROLE_形式
            List<GrantedAuthority> authorities = new ArrayList<>();
            authorities.add(new SimpleGrantedAuthority("ROLE_" + user.getRole()));

            //从数据库中取回的对象加上权限
            return new User(
                    user.getUsername(),
                    user.getPassword(),//这里的密码是数据库中加密后的
                    authorities
            );
        }
}

```



5.用户查找完毕后，然后与 configure(AuthenticationManagerBuilder auth) 定义的规则进行匹配，成功就可以通过验证，此时userDetailService为数据库查出的对象，密码因为在添加的时候带了加密功能，所以要在passwordEncoder配置密码规则  auto .passwordEncoder(new MyPasswordEncoder()，这样，页面提交时候，密码以明文的方式进行匹配。和用户的输入方式正好匹配

