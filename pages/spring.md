
## Simple set up with Spring Security and Spring Boot

Simple in-memory authentication with special rules for several endopoints (local only)

```
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    ...

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth
            .inMemoryAuthentication()
            .withUser("user").password(user_password).roles("USER")
        .and()
            .withUser("admin").password(admin_password).authorities("ADMIN");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf()
            .disable()
            .httpBasic()
         .and()
            .authorizeRequests()
            .antMatchers("/admin/config/decrypt").access("hasIpAddress('127.0.0.1/32') and hasAuthority('ADMIN')")
            .antMatchers("/admin/config/encrypt").access("hasIpAddress('127.0.0.1/32') and hasAuthority('ADMIN')")
            .antMatchers("/admin/config/**").authenticated()
            .antMatchers("/**").permitAll()
        ;
    }
}
```
To use IPV4 instead of IPV6 use this:

```
-Djava.net.preferIPv4Stack=true
```

## Use two different DataSources

TODO

## Spring DSL in Spring Integration

TODO