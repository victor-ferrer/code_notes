## Spring Batch: Use seconday datasource for metadata

On top of [this example](/spring/core/#use-two-different-datasources), add this @Configuration class:

```
import org.springframework.batch.core.configuration.annotation.DefaultBatchConfigurer;

// Another @Configuration class...

@Autowired
@Qualifier("jobsDataSource")
private DataSource dataSource;

@Bean
public BatchConfigurer configurer()
{
    // This is required to avoid problems when jobs datasource is into some secondary datasource.
    return new DefaultBatchConfigurer(dataSource);
}
```

