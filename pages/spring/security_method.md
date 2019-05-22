To enable `@PreAuthorize` requests to check user roles before allowing certain methods to be executed, this setup is needed:

This configuration class enables Method Security and looks for @PathVariable annotation to bind parameters:
```
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfiguration  extends GlobalMethodSecurityConfiguration{
	
    @Override
    protected MethodSecurityExpressionHandler createExpressionHandler() {
        DefaultMethodSecurityExpressionHandler result = new DefaultMethodSecurityExpressionHandler();
        result.setParameterNameDiscoverer(new AnnotationParameterNameDiscoverer(PathVariable.class.getName()));
        return result;
    }	
}
```
Then, in order to use this in the code:

```
@PreAuthorize("hasRole('ADMIN_' + #customer )")
@DeleteMapping(path = "/{customer }")
public void delete( @PathVariable("customer") String customer) {
	// Business logic code doing something with customer $customer 
}
```

Another option is to inject the `Principal` and use a Bean reference:

```
@PreAuthorize("@accessService.check(#user, #customer)")
@DeleteMapping(path = "/{customer }")
public void delete( @PathVariable("customer") String customer, @P("user") Principal user) {
	// Business logic code doing something with customer $customer 
}
```

	   