## How to use dynamic profiles 

In order to use `@Configuration` classes with dynamic profiles, one option is to negate the profiles in which we do not want those classes to be activated.

However, there is an issue with `@Profile("!dev")` and the rest of the annotations are still picked up (like `@EnableXXX`)

To overcome this issue, this `@Condition` can be used:

```
public abstract class ProfileCondition extends SpringBootCondition {
	
	/**
	 * Once implemented, this method will have to assess whether this {@link ProfileCondition} is met give the provided {@link Profile}
	 * @param environment
	 * @return
	 */
	protected abstract boolean matchProfiles(final Environment environment);
	
    @Override
    public ConditionOutcome getMatchOutcome(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        if (matchProfiles(conditionContext.getEnvironment())) {
            return ConditionOutcome.match("A local profile has been found.");
        }
        return ConditionOutcome.noMatch("No local profiles found.");
    }
}
```

This implementation matches a production setup (no `dev` and no `test`):

```
public class ProdProfileCondition extends ProfileCondition {
	   protected boolean matchProfiles(final Environment environment) {    
	        return Arrays.stream(environment.getActiveProfiles()).anyMatch(prof -> {
	            return !prof.equals("dev") && !prof.equals("test");
	        });
	    }
	}
``` 

Finally, in order to activate it:
```
@Configuration
@Conditional(value = {ProdProfileCondition.class})
@EnableRedisHttpSession
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
```