Caching with EHCache in Spring-Boot

Dependencies needed

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
	<groupId>javax.cache</groupId>
	<artifactId>cache-api</artifactId>
</dependency>
<dependency>
	 <groupId>org.ehcache</groupId>
	 <artifactId>ehcache</artifactId>
	<version>${ehcache.version}</version>
</dependency>
```

Spring Configuration Class

```
@Configuration
@EnableCaching
public class CacheConfiguration {

    // Implement your own key generator if needed
	@Bean
	public KeyGenerator requestKeyGenerator() {
		return new KeyGenerator() {

			@Override
			public Object generate(Object target, Method method, Object... params) {...}
		}}
	
	// Implement a bean exposing a boolean method checking whether a request should be cached
	@Bean
	public CacheControl cacheControl() {
		return new cacheControl();
	}
	
	
	static class CacheControl {
		public boolean checkIfCachingIsNeeded(BusinessRequest request) {...}}

}
```

How to mark a method as `Cacheable`:

```
@Cacheable(cacheNames="my_cache", keyGenerator="requestKeyGenerator",condition="@cacheControl.checkIfCachingIsNeeded(#a0)")
public BusinessResponse execute(BusinessRequest request){
...
}
```

Note that `BusinessResponse` should be serializable so it can be stored in the different cache levels.

EHCache XML configuration

```
<config xmlns='http://www.ehcache.org/v3'
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:jsr107="http://www.ehcache.org/v3/jsr107"
        xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core-3.5.xsd
							http://www.ehcache.org/v3/jsr107 http://www.ehcache.org/schema/ehcache-107-ext-3.5.xsd">

	<persistence directory="./target"/>
	
    <cache alias="my_cache" >
    
        <key-type copier="org.ehcache.impl.copy.SerializingCopier">java.lang.String</key-type>
		<value-type copier="org.ehcache.impl.copy.SerializingCopier">org.mycompany.bussiness.BusinessResponse</value-type>
    
    	<!--Entries in this cache will not expire -->
        <expiry>
            <none/>
        </expiry>
        
        <!-- Cache entries will be stored in head memory, then off heap RAM and finally disk
        	 Most used records will be in heap, then RAM and the least used will go to disk
         -->
        <resources>
	        <heap unit="MB">512</heap>
        	<offheap unit="MB">2048</offheap>
        	<disk unit="MB">5096</disk>
        </resources>
        <jsr107:mbeans enable-statistics="true"/>
    </cache>
</config>
```


