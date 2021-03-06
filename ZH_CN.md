# jpa-querydsl-spring-boot-starter
中文 | [English](./README.md)
* Jpa Quick Design
* 支持spring boot 2.1.x 以上的版本(包含2.1.x)
### 简介
* Jpa Service CRUD 封装,支持querydsl开箱即用。
### 使用
1. 添加依赖
     ```xml
        <dependency>
            <groupId>com.github.uinio</groupId>
            <artifactId>jpa-querydsl-spring-boot-starter</artifactId>
            <version>2.5.1</version>
        </dependency>
      ```
----------   
2. 示例
* 实体类
```java
@Table
@Entity
public class Contact  implements Serializable {

   @Id
   private Integer id;
  
   private Name name;
  
   private String notes;
   
   //getter setter ...      
}
```
---------
> 仓库
```java
public interface ContactRepository extends JpaRepository<Contact, Integer>{
    
}
```
--------
> 服务
  * 提供单表CURD操作
  * 注意:提供的JpaService仅支持单表操作,复杂操作使用EntityManager或者QueryDsl
```java
public interface ContactService extends JpaService<Contact, Integer> {
}
```
--------
```java
@Service
public class  ContactServiceImpl extends JpaServiceImpl<Contact, Integer> implements UserService {
    
}
```
-------
> 测试
```java
@SpringBootTest
class MainApplicationTests {

    @Autowired
    private ContactService contactService;

    @Test
    void contextLoads() {
       //新增
       Contact contact = new Contact();
       contact.setName("test");
       contact.setNotes("test");
       contactService.save(contact);

       //更新
       Contact contact = new Contact();
       contact.setId(1);
       contact.setName("example");
       contact.setNotes("example");
       contactService.update(contact);

       //删除
       contactService.deleteById(1);

       //批量删除
       contactService.deleteByIds(new Integer[]{1,2});

       //分页
       contactService.page(1, 2);

       //...
    }
}
```
-------
> querydsl 开箱即用

* 配置
```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>com.mysema.maven</groupId>
                <artifactId>apt-maven-plugin</artifactId>
                <version>1.1.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>process</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>target/generated-sources/java</outputDirectory>
                            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                        </configuration>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>com.querydsl</groupId>
                        <artifactId>querydsl-apt</artifactId>
                        <version>${querydsl.version}</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
```
* mvn compile 生成query类
```java
@SpringBootTest
class MainApplicationTests {

    @Autowired
    private JPAQueryFactory jpaQueryFactory;

    @Test
    void contextLoads() {
       QContact contact = QContact.contact;
       jpaQueryFactory.update(contact).set(contact.name,"test")
        .where(contact.id.eq(1)).execute();
    }

}
```

