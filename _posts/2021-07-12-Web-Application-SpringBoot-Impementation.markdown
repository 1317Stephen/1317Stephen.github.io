---
# layout: posts
title: "[SpringBoot/Vue.js Project] Blog Implementation - Spring Boot"
date: 2021-07-12 22:02:14 +0900
categories:
  - Backend
tags:
  - Spring Boot
  - Oracle ATP Database
  - Oracle Cloud
  - Backend
  - Database
excerpt: "Blog Web Application implementation of Backend(SpringBoot)."
toc: true
---

## Backend Setting
### `application.properties`
{% highlight java %}
server.port = 1111 
spring.datasource.driver-class-name=oracle.jdbc.pool.OracleDataSource
spring.datasource.url=YOUR_ORALCE_WALLET_TNSNAME_AND_DIRECTORY
spring.datasource.username=YOUR_ID
spring.datasource.password=YOUR_PASSWORD
oracle.ucp.minPoolSize=5
oracle.ucp.maxPoolSize=50
spring.main.allow-bean-definition-overriding=true
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
logging.level.com.example.demo=debug
{% endhighlight%}

## Config Package
### `MongoConfig` Class
: Embedded Database Setting (Mongo DB)
{% highlight java %}
@Profile(value = "mongo")
@Configuration
@Import(EmbeddedMongoAutoConfiguration.class)
public class MongoConfig {
}
{% endhighlight %}

### `OracleConfig` Class
: External Database Setting (Oracle ATP DB)
{% highlight java %}
@Profile(value = "oracle")
@Configuration
public class OracleConfig {
    @Value("${spring.datasource.url}")
    private String url;
    @Value("${spring.datasource.username}")
    private String username;
    @Value("${spring.datasource.password}")
    private String password;
    @Value("${oracle.ucp.minPoolSize}")
    private String minPoolSize;

    @Value("${oracle.ucp.maxPoolSize}")
    private String maxPoolSize;

    @Value("${spring.datasource.driver-class-name:oracle.jdbc.pool.OracleDataSource}")
    private String driverClassName;

    @Bean(name = "OracleUniversalConnectionPool")
    @Primary
    public DataSource getDataSource() {
        PoolDataSource pds = null;
        try {
            pds = PoolDataSourceFactory.getPoolDataSource();

            pds.setConnectionFactoryClassName(driverClassName);
            pds.setURL(url);
            pds.setUser(username);
            pds.setPassword(password);
            pds.setMinPoolSize(Integer.valueOf(minPoolSize));
            pds.setInitialPoolSize(10);
            pds.setMaxPoolSize(Integer.valueOf(maxPoolSize));

        } catch (SQLException ea) {
            System.err.println("Error connecting to the database: " + ea.getMessage());
        }

        return pds;
    }
}
{% endhighlight %}

### `WebConfig` Class
: Fronted Connection Setting
{% highlight java %}
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        String exampleAddress = "http://127.0.0.1:9999";  //This is an test address.(Maybe your frontend address.)
        registry.addMapping("/**")
                .allowedOrigins(exampleAddress);
    }
}
{% endhighlight %}


## ApiResponse Package
### `ApiResponse` Abstract
{% highlight java %}
@Getter @Setter
@RequiredArgsConstructor
public abstract class ApiResponse<T> {
    @NonNull private T data;
    private List<String> errors;
}
{% endhighlight %}


## Main API Packages
### `Item` Class
 : `Model` of the API, mostly from Oracle ATP Database. (Below is the partial of `ArticleItem` Class.)
{% highlight java %}
@Entity
@Data
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Table(name="Article")
public class ArticleItem {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "Id")
    private String id;

    @Column(name="Title")
    private String title;

    @Column(name="Contents")
    private String contents;

    @Column(name="Created_at")
    @CreationTimestamp
    private LocalDateTime created_at;

    @Column(name="Modified_at")
    @UpdateTimestamp
    private LocalDateTime modified_at;

    @Column(name = "Menu_id")
    private String menu_id;

}
{% endhighlight %}

### `Adapter` Class
: Based in the `@Builder` of the `Item` Class, `Adapter` Class build the Response and Item(Model).
{% highlight java %}
public class ArticleItemAdapter {
    public static ArticleItemResponse articleItemResponse(final ArticleItem articleItem, final List<String> errors) {
        return ArticleItemResponse.builder()
                .articleItem(articleItem)
                .errors(Optional.ofNullable(errors).orElse(new ArrayList<>()))
                .build();
    }

    public static ArticleItem articleItem(final ArticleItemRequest articleItemRequest) {
        if(articleItemRequest == null) {
            return null;
        }

        return ArticleItem.builder()
                .title(articleItemRequest.getTitle())
                .contents(articleItemRequest.getContents())
                .created_at(articleItemRequest.getCreated_at())
                .modified_at(articleItemRequest.getModified_at())
                .menu_id(articleItemRequest.getMenu_id())
                .build();
    }
}
{% endhighlight %}


### `Repository` Class
: Below is the `Repository` from Oracle ATP Database.
{% highlight java %}
@Repository
public interface ArticleItemRepository extends JpaRepository<ArticleItem, String>{
}

{% endhighlight %}

### `Controller` Class
: URL Resolution for `get`, `getAll`, `create`, `delete` and `update`.
{% highlight java %}
@RestController
@RequestMapping("/article")
public class ArticleItemController {
    @Autowired
    private ArticleItemService articleItemService;

    @RequestMapping(method = RequestMethod.GET, value = "/{id}")
    public @ResponseBody
    ArticleItemResponse get(@PathVariable(value = "id") String id) {
        List<String> errors = new ArrayList<>();
        ArticleItem articleItem = null;
        try {
            articleItem = articleItemService.get(id);
        } catch (final Exception e) {
            errors.add(e.getMessage());
        }
        return ArticleItemAdapter.articleItemResponse(articleItem, errors);
    }

    @RequestMapping(method = RequestMethod.GET)
    public @ResponseBody List<ArticleItemResponse> getAll() {
        List<String> errors = new ArrayList<>();
        List<ArticleItem> articleItems = articleItemService.getAll();
        List<ArticleItemResponse> articleItemResponse = new ArrayList<>();

        articleItems.stream().forEach(articleItem -> {
            articleItemResponse.add(ArticleItemAdapter.articleItemResponse(articleItem, errors));
        });
        return articleItemResponse;
    }

    @RequestMapping(method = RequestMethod.POST)
    public @ResponseBody
    ArticleItemResponse create(@RequestBody final ArticleItemRequest articleItemRequest) {
        List<String> errors = new ArrayList<>();
        ArticleItem articleItem = ArticleItemAdapter.articleItem(articleItemRequest);
        try {
            articleItem = articleItemService.create(articleItem);
        } catch (final Exception e) {
            errors.add(e.getMessage());
        }
        return ArticleItemAdapter.articleItemResponse(articleItem, errors);
    }

    @RequestMapping(method = RequestMethod.DELETE, value = "/{id}")
    public @ResponseBody void delete(@PathVariable(value = "id") String id) {
        List<String> errors = new ArrayList<>();

        try {
            articleItemService.delete(id);
        } catch (final Exception e) {
            errors.add(e.getMessage());
        }
    }

    @RequestMapping(method = RequestMethod.PUT, value = "/{id}")
    public @ResponseBody ArticleItemResponse update(@PathVariable(value = "id") String id, @RequestBody final ArticleItemRequest articleItemRequest) {
        List<String> errors = new ArrayList<>();
        ArticleItem articleItem = ArticleItemAdapter.articleItem(articleItemRequest);
        try {
            articleItem = articleItemService.update(id, articleItem);
        } catch (final Exception e) {
            errors.add(e.getMessage());
        }
        return ArticleItemAdapter.articleItemResponse(articleItem, errors);
    }

}

{% endhighlight %}

### `Service` Class
: `get`, `getAll`, `create`, `delete` and `update` methods using `Repository`.
{% highlight java %}
@Service
public class ArticleItemService {
    @Autowired
    private ArticleItemRepository articleItemRepository;

    public ArticleItem get(final String id) { return articleItemRepository.findById(id).orElse(null); }

    public List<ArticleItem> getAll() {
        return articleItemRepository.findAll();
    }

    public void delete(final String id) { articleItemRepository.deleteById(id); }

    public ArticleItem create(final ArticleItem articleItem) {
        if(articleItem == null) {
            throw new NullPointerException("Menu Item cannot be null!");
        }
        return articleItemRepository.save(articleItem);
    }

    public ArticleItem update(final String id, final ArticleItem articleItem) {
        if(!articleItemRepository.existsById(id)) {
            throw new NullPointerException("Article Item ID is NULL.");
        }
        ArticleItem existArticleItem = articleItemRepository.findById(id).orElse(null);


        existArticleItem.setTitle(articleItem.getTitle());
        existArticleItem.setContents(articleItem.getContents());
        existArticleItem.setMenu_id(articleItem.getMenu_id());

        existArticleItem = articleItemRepository.save(existArticleItem);

        return existArticleItem;
    }

}
{% endhighlight %}

### `Response` Class
: This class extends the `ApiResponse` Abtract from `ApiResponse` package.
{% highlight java %}
public class ArticleItemResponse extends ApiResponse<ArticleItem> {
    @Builder
    public ArticleItemResponse(final ArticleItem articleItem, final List<String> errors) {
        super(articleItem);
        this.setErrors(errors);
    }
}
{% endhighlight %}

### `Request` Class
{% highlight java %}
@Setter @Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ArticleItemRequest {
    private String id;
    private String title;
    private String contents;
    private LocalDateTime created_at;
    private LocalDateTime modified_at;
    private String menu_id;
}
{% endhighlight %}