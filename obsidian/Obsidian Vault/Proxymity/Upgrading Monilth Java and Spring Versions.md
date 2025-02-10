Trying to compile the project with Java 11, gives the following issues:
java: as of release 10, 'var' is a restricted local variable type and cannot be used for type declarations or as the element type of an array

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/StringUtils.java:19:46
java: package sun.security.krb5.internal.ktab does not exist

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/StringUtils.java:19
java: static import only from classes and interfaces

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/DateTimeUtils.java:6:56
java: package com.sun.org.apache.xerces.internal.jaxp.datatype does not exist

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/intermediary/Dto/IntermediaryDto.java:18:20
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/entity/LeanBeneficialOwner.java:33:21
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:53:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:55:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:57:29
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:59:31
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:63:20
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:67:20
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:123:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:125:27
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/model/meeting/MeetingDto.java:129:18
java: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/Triplet.java:7
java: Not generating hashCode: One of equals or hashCode exists. You should either write both of these or none of these (in the latter case, lombok generates them).

/Users/miron/Dev/proxymity.server/common.lib/src/main/java/com/citi/xvote/utils/DateTimeUtils.java:6:56
java: package com.sun.org.apache.xerces.internal.jaxp.datatype does not exist

**When compilation is showing weird errors - remember to check the JAVA_HOME env var for correct JDK!!!**

springframework.integration 5.x -> 6.x:
```
import org.springframework.integration.handler.GenericHandler; -> 
import org.springframework.integration.core.GenericHandler;
```

spring cloud stream - need to properly handle binding od deprecated annotated binding (@Input, @Output, @EnableBinding) as described in:
https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_bindings
https://presearch.com/search?q=how%20to%20migrate%20spring%20messaging%20%40Output%20annotations%20to%20functional%20programming
https://stackoverflow.com/questions/59788479/org-springframework-cloud-stream-messaging-source-cant-be-autowired
https://github.com/jhipster/generator-jhipster/issues/20297
https://presearch.com/search?q=import+org.springframework.cloud.stream.annotation.Input%3B+not+found

Currently - I simply commented the annotations so that it would compile!

remove `import lombok.var`

```
KafkaHeaders.RECEIVED_MESSAGE_KEY ->
RECEIVED_KEY
```

```
responseEntity.getStatusCode() ->
(HttpStatus) responseEntity.getStatusCode()
```

```
import javax.persistence.*; ->
import jakarta.persistence.*;
```

String formatting, multiple line strings...

```
import javax.persistence.Column;
import javax.persistence.Convert;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Index;
import javax.persistence.Table;
import javax.persistence.Transient;
->
import jakarta.persistence.Column;
import jakarta.persistence.Convert;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Index;
import jakarta.persistence.Table;
import jakarta.persistence.Transient;
```



Spring Boot 3.x has removed `WebSecurityConfigurerAdapter` as part of the Spring Security 6.x changes. This class has been deprecated and replaced with a more modern and declarative approach to configuring security using beans and lambdas.

### Solution: Replace `WebSecurityConfigurerAdapter`

Instead of extending `WebSecurityConfigurerAdapter`, you now define a `SecurityFilterChain` bean in your configuration class. Here's an example of how to migrate your code:

#### Old Style (Spring Boot 2.7)

java

Copy code

`import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter; import org.springframework.security.config.annotation.web.builders.HttpSecurity;  @Configuration public class SecurityConfig extends WebSecurityConfigurerAdapter {      @Override     protected void configure(HttpSecurity http) throws Exception {         http             .authorizeRequests()                 .antMatchers("/public/**").permitAll()                 .anyRequest().authenticated()             .and()             .formLogin();     } }`

#### New Style (Spring Boot 3.x)

java

Copy code

`import org.springframework.context.annotation.Bean; import org.springframework.context.annotation.Configuration; import org.springframework.security.config.annotation.web.builders.HttpSecurity; import org.springframework.security.web.SecurityFilterChain;  @Configuration public class SecurityConfig {      @Bean     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {         http             .authorizeHttpRequests(authorize -> authorize                 .requestMatchers("/public/**").permitAll()                 .anyRequest().authenticated()             )             .formLogin();         return http.build();     } }`

### Key Changes

1. **No Need to Extend a Base Class**:
    
    - `WebSecurityConfigurerAdapter` is removed, and you no longer need to subclass it.
    - Use the `@Bean` method to define a `SecurityFilterChain`.
2. **Use `requestMatchers` instead of `antMatchers`**:
    
    - `requestMatchers` is more flexible and aligns with modern Spring Security patterns.
    - `antMatchers` is still available for simple path matching, but it's recommended to migrate.
3. **Declarative Lambda Style**:
    
    - Configuration is written in a functional style with lambdas, making it cleaner and more expressive.
4. **Explicit Return of `http.build()`**:
    
    - The `HttpSecurity` object must be built into a `SecurityFilterChain`.

By switching to this approach, you'll align with Spring Boot 3.x and Spring Security 6.x best practices. Let me know if you encounter further issues during migration!



## XMLGregorianCalander

```
XMLGregorianCalendar defaultTime = new XMLGregorianCalendarImpl(); ->
try {  
    XMLGregorianCalendar defaultTime = DatatypeFactory.newInstance().newXMLGregorianCalendar();
    ...  
```

`IntegrationFlow.from() -> IntegrationFlows.from()`

Commented the Autowired gateway in KafkaCompanyAccountConfig -> now there is no bean error and app runs ok??? Same in KafkaHoldingConfig














---

#### Work log (6d)

~3d - OpenRewrite automatic migrations + a lot of manual refactoring and migrations:
* Java 21 + Boot 3.3
* Springfox -> Open API
* Cloud stream declerative model -> functional model (All manual, a lot of work and changes. @Gateway / MessageGateway not migrated yet)
* HttpClient -> HttpClient5 migration
* A lot of smaller migrations:
	* javax -> jakarta migrtion
	* jetbrain annotations -> jakarta annotations (why use jetbrain???)

~1d app is not launching/running. bean creation issue. tried to figure that out.
~0.5d dev branch of Java 8 (original code) not running locally, why? It looks like none of the backend developers are running it locally. To debug they use tests (!!!)
~1.5d integration tests not working locally. Sessions with Konstantin and Yoni didn't help. Took me 1.5d to find out the issue and make tests run locally. Still the app itself is not running locally, so no point in checking if the migrated app is running locally...
	*WED EOD* update: finally was able to run integrations tests on the original code. ~ 600 tests run before Mac got stuck but at least I can see them running and can run them individually.

**currently stuck in the migrated app on making either the app run or the integration tests work**

Wonder if it was better to modernize the app before migration:
- Cloud stream functional model (XXL)
- Springfox -> Open API (L)
- HttpClient 5 (M-L)

Or: remove all old sources and add them in groups to find out the bean creation cause. I suspect the DB/repository beans, but have no idea how to assert this. XXL very time consuming...


* try running local build on sandbox - end of june
* open teams upgrade forum
* AWS architects
* AWS Q
