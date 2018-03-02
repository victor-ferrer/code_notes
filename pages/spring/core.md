## Use two different DataSources

To use more than the predefined DataSource in Spring, for instance an H2 Database:

```
### Add this new set of properties ###

## URL used to connect to the H2 Database
spring.secondDatasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
## Driver class used to connect to the H2 database (it will depend on datasource).
spring.secondDatasource.driver-class-Name=org.h2.Driver
## User name 
spring.secondDatasource.username=xxx
## Password 
spring.secondDatasource.password=xxx
## Datasource configuration for jobs database.
spring.jpa.hibernate.ddl-auto=create-drop
spring.secondDatasource.initialize=true
spring.secondDatasource.test-on-borrow=true
spring.secondDatasource.validation-query=select 1

```

Add this @Configuration class:

```
/**
 * Config class holding several datasources, the default one and the H2
 */
@Configuration
public class DataSourceConfiguration
{
    @Bean
    @Qualifier("businessDataSource")
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource primaryDataSource()
    {
        return DataSourceBuilder.create().build();
    }

    @Bean
    @Primary
    @Qualifier("jobsDataSource")
    @ConfigurationProperties(prefix = "spring.secondDatasource")
    public DataSource secondaryDataSource()
    {
        return DataSourceBuilder.create().build();
    }
}
``` 
