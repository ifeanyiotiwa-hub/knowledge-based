Manually defining datasource Bean

```java

    import javax.sql.DataSource;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.core.env.Environment;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
    import org.springframework.jdbc.datasource.DataSourceTransactionManager;
    import org.springframework.transaction.PlatformTransactionManager;
    .
    .
    .
    

    @Bean
    public PlatformTransactionManager entityTransactionalManager(Environment environment) {
        return new DataSourceTransactionManager(entityDataSource(environment));
    }

    @Bean(ENTITY_JDBC_TEMPLATE)
    public JdbcTemplate entityJdbcTemplate(Environment environment) {
        return new JdbcTemplate(entityDataSource(environment));
    }

    @Bean(ENTITY_NAMED_JDBC_TEMPLATE)
    public NamedParameterJdbcTemplate cashoutNameJdbcTemplate(Environment environment) {
        return new NamedParameterJdbcTemplate(entityDataSource(environment));
    }

    @Bean(destroyMethod = "close")
    public DataSource entityDataSource(Environment environment) {
        final HikariConfig config = new HikariConfig();
        config.setDriverClassName(com.mysql.cj.jdbc.Driver.class.getName());
        config.setJdbcUrl(environment.getProperty("spring.entity.datasource.url"));
        config.setAutoCommit(true);
        config.setUsername(environment.getRequiredProperty("spring.entity.datasource.username"));
        config.setPassword(environment.getRequiredProperty("spring.entity.datasource.password"));
        config.setMaximumPoolSize(environment.getProperty("spring.entity.datasource.hikari.maximum-pool-size", Integer.class, 20));
        config.setMinimumIdle(environment.getProperty("spring.entity.datasource.hikari.minimum-idle", Integer.class, 20));
        config.addDataSourceProperty("cachePrepStmts", environment.getProperty("spring.entity.datasource.hikari.data-source-properties.cachePrepStmts", "true"));
        config.addDataSourceProperty("prepStmtCacheSize", environment.getProperty("spring.entity.datasource.hikari.data-source-properties.prepStmtCacheSize", "250"));
        config.addDataSourceProperty("prepStmtCacheSqlLimit", environment.getProperty("spring.entity.datasource.hikari.data-source-properties.prepStmtCacheSqlLimit", "2048"));
        config.setLeakDetectionThreshold(15000);

        return new HikariDataSource(config);
    }


```
