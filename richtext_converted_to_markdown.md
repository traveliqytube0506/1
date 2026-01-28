🍃 Spring Boot Syllabus Notes (Hinglish Edition)Short explanations aur quick code snippets ke saath complete syllabus coverage.1. Creating Your First Spring Boot ApplicationConcept: Spring Boot setup karna aur run karna.Spring Initializr: Project generate karne ke liye best tool (start.spring.io).Maven Structure: pom.xml mein dependencies hoti hain.Embedded Tomcat: Server alag se install nahi karna padta, Spring Boot mein inbuilt hota hai.Code:@SpringBootApplication // Configuration + EnableAutoConfiguration + ComponentScan
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args); // App start yahan se hoti hai
    }
}
2. Spring Boot Configuration & PropertiesConcept: App ki settings change karna bina code change kiye.File: application.properties ya application.yml.Database URL, Port, Secrets yahan define karte hain.Code (application.properties):server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
app.jwt.secret=MySecretKey123
3. Dependency Injection, IoC & LombokConcept: Objects hum nahi banate (new keyword), Spring banata hai.IoC: Control Spring ke paas hai.DI: Spring objects inject karta hai.@RequiredArgsConstructor (Lombok): Constructor Injection ka shortcut. @Autowired likhne ki zaroorat nahi padti agar field final ho.Code:@Service
@RequiredArgsConstructor // Lombok: Saare final fields ka constructor auto-generate karega
public class UserService {
    
    private final UserRepository userRepo; // Spring automatically inject karega
    
    // Manual constructor likhne ki zaroorat nahi
}
4. Spring Boot Web DevelopmentConcept: API endpoints banana.@RestController: Class ko API handler banata hai.@GetMapping, @PostMapping: URL ko method se map karte hain.Code:@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return new User(id, "Rahul"); // JSON return hoga
    }
}
5. RESTful API Design PrinciplesBest Practices:GET: Data lene ke liye.POST: Naya data create karne ke liye.PUT: Data update karne ke liye.DELETE: Data remove karne ke liye.Status Codes: 200 OK, 201 Created, 404 Not Found, 500 Server Error.6. Spring Data JPA & Custom Queries (@Query)Concept: SQL queries likhe bina DB operations, aur complex queries ke liye @Query.JPQL: Java objects pe query chalti hai (Table name nahi, Class name use hota hai).Native Query: Pure SQL query.Code:public interface EmpRepo extends JpaRepository<Employee, Long> {
    
    // 1. Method Name Strategy (Spring khud query banayega)
    List<Employee> findByName(String name);

    // 2. JPQL (Custom Query on Object)
    @Query("SELECT e FROM Employee e WHERE e.email = ?1")
    Employee findByEmailJPQL(String email);

    // 3. Native SQL (Pure Database Query)
    @Query(value = "SELECT * FROM employee_table WHERE city = :city", nativeQuery = true)
    List<Employee> findByCityNative(@Param("city") String city);
}
7. Database Integration (SQL/Mongo)Concept: application.properties mein URL aur credentials set karo. Spring Boot driver detect karke connection bana leta hai.8. Spring Security BasicsConcept:Authentication: Login (Who are you?).Authorization: Permissions (Can you access this?).Code:@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.csrf().disable()
        .authorizeHttpRequests()
        .requestMatchers("/public/**").permitAll() 
        .anyRequest().authenticated(); 
    return http.build();
}
9. JWT Authentication & AuthorizationConcept: Login ke baad server "Token" deta hai. Stateless mechanism.10. API Validation (@Valid, @NotNull, @NotBlank)Concept: Data save karne se pehle validate karna.@Valid: Controller mein lagta hai taaki DTO check ho.Annotations:@NotNull: Null nahi ho sakta (lekin empty string chalega).@NotBlank: Null bhi nahi aur khali string ("") bhi nahi chalega.@Size: Length control karne ke liye.@Min/@Max: Numbers ki range ke liye.@Pattern: Regex ke liye (e.g., phone number).Code (DTO):public class UserDTO {
    @NotBlank(message = "Name khali nahi ho sakta")
    private String name;

    @Email(message = "Email valid hona chahiye")
    private String email;

    @Size(min = 6, max = 20, message = "Password 6-20 chars ka hona chahiye")
    private String password;
    
    @Min(value = 18, message = "Age 18+ honi chahiye")
    private int age;
}
Code (Controller):@PostMapping
public String createUser(@RequestBody @Valid UserDTO user) { // @Valid trigger karega checks ko
    return "Saved";
}
11. Advanced DB & Pagination (Pageable)Concept: Large data ko pages mein todna.PageRequest.of(pageNumber, pageSize, Sort) use hota hai.Page<T> return type hota hai jo data ke saath total pages ka metadata bhi deta hai.Code:// Controller
@GetMapping
public Page<Product> getProducts(@RequestParam int page, @RequestParam int size) {
    // Page index 0 se start hota hai
    Pageable pageable = PageRequest.of(page, size, Sort.by("price").descending());
    return productRepo.findAll(pageable);
}

// Repository
Page<Product> findAll(Pageable pageable); // JpaRepository mein already hota hai
12. Object Mapping (MapStruct / @Mapper)Concept: Entity ko DTO mein aur DTO ko Entity mein convert karna. Manual getters/setters likhne se bachne ke liye.MapStruct: Automatically code generate karta hai compile time pe.Code:@Mapper(componentModel = "spring") // Spring Bean banayega
public interface UserMapper {
    
    // Field names same hone chahiye, warna @Mapping lagana padega
    UserDTO toDto(User user);
    
    User toEntity(UserDTO userDto);
}

// Usage in Service
@Autowired
private UserMapper mapper;

public UserDTO getUser(Long id) {
    User user = repo.findById(id).get();
    return mapper.toDto(user); // Automatic conversion
}
13. API Documentation (Swagger/OpenAPI)Dependency: springdoc-openapi-starter-webmvc-ui.URL: http://localhost:8080/swagger-ui/index.html.14. Caching & Optimization@Cacheable: DB load kam karne ke liye.@Cacheable("users")
public User getUser(Long id) { ... }
15. Asynchronous Processing@Async: Background task run karne ke liye.16. File UploadMultipartFile: File receive karne ke liye use hota hai.17. Email & NotificationsJavaMailSender: SMTP integration ke liye.18. Microservices & CommunicationConcepts: FeignClient, RestTemplate, API Gateway.19. Webhooks & Payment IntegrationFlow: Initiate -> Gateway -> Webhook (Server Notification) -> Update DB.
