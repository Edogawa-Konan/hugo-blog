---
title: "Springboot"
date: 2019-11-21T09:58:19+08:00
lastmod: 2019-11-21T09:58:19+08:00
draft: false
tags: ['Springboot']
categories: ['Java']
---

# springboot

## 注解

### @qualifier

`@Qualifier`注解和`@Autowired`注解搭配使用，因为`@Autowired`注解**按照类型装配**，所以如果有两个相同类型的bean，会产生错误。故而搭配`@Qualifier`注解，来标示bean的名字。

针对三种注入方式，写法如下：

```java
public Payroll {
   @Autowired @Qualifier("employee") private final Person person;
}
```

```java
public Payroll {
   private final Person person;
   @Autowired
   @Qualifier("employee")
   public void setPerson(Person person) {
     this.person = person;
   } 
}
```

```java
public Payroll {

    private Person person;

    @Autowired
    public Payroll(@Qualifier("employee") Person person){
          this.person = person;
    }

}
```

### @Resource vs @autowired

- `@Autowired` and `@Inject`
  1. Matches by Type
  2. Restricts by Qualifiers
  3. Matches by Name
- `@Resource`
  1. Matches by Name
  2. Matches by Type
  3. Restricts by Qualifiers (ignored if match is found by name)

简而言之，@Autowired按照类型装配，而@Resource则按照名称，但是当未显式指定名称且按照名称找不到的时候，会和@Autowired一样。

@Resource只支持**field和setter方法**注入。

未指定名称时，其名称取自field的名字。

[Injection with @Resource](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-resource-annotation)

## 基本配置

Profile用来针对不同的环境下使用不同的配置文件，多环境配置文件必须以`application-{profile}.properties`的格式命，其中`{profile}`为环境标识。比如定义两个配置文件：

- application-dev.properties：开发环境

  ```
  server.port=8080
  ```

- application-prod.properties：生产环境

  ```
  server.port=8081
  ```

至于哪个具体的配置文件会被加载，需要在application.properties文件中通过`spring.profiles.active`属性来设置，其值对应`{profile}`值。

如：`spring.profiles.active=dev`就会加载application-dev.properties配置文件内容。可以在运行jar文件的时候使用命令`java -jar xxx.jar --spring.profiles.active={profile}`切换不同的环境配置。

## Mybatis

MyBatis-Spring-Boot-Starter will:

- Autodetect an existing `DataSource`
- Will create and register an instance of a `SqlSessionFactory` passing that `DataSource` as an input using the `SqlSessionFactoryBean`
- Will create and register an instance of a `SqlSessionTemplate` got out of the `SqlSessionFactory`
- Auto-scan your mappers, link them to the `SqlSessionTemplate` and register them to Spring context so they can be injected into your beans

The MyBatis-Spring-Boot-Starter will search, by default, for mappers marked with the `@Mapper` annotation.

如果不在默认根目录下，可以用`@MapperScan`注解。

## JPA

> The goal of the Spring Data repository abstraction is to significantly reduce the amount of boilerplate(模板) code required to implement data access layers for various persistence stores.

JPA诞生的缘由是为了整合第三方ORM框架，建立一种标准的方式。在ORM框架中，Hibernate是一支很大的部队，使用很广泛，也很方便，能力也很强。可以认为JPA是标准，事实上也是，JPA几乎都是接口，实现都是Hibernate在做。而Spring-data-jpa，就是与jpa的整合。

在使用持久化工具的时候，一般都有一个对象来操作数据库，在原生的Hibernate中叫做Session，在JPA中叫做EntityManager，在MyBatis中叫做SqlSession，通过这个对象来操作数据库。

### 核心用法

spring-data-jpa的核心接口抽象是`Repository`，其作为一个标记接口。类型参数是domain class的类型以及其ID type。

```java
@Indexed
public interface Repository<T, ID> {
}
```

`CrudRepository`接口继承了这个借口，提供了详尽的CRUD功能。

```java
@NoRepositoryBean
public interface CrudRepository<T, ID> extends Repository<T, ID> {

	/**
	 * Saves a given entity. Use the returned instance for further operations as the save operation might have changed the
	 * entity instance completely.
	 *
	 * @param entity must not be {@literal null}.
	 * @return the saved entity; will never be {@literal null}.
	 */
	<S extends T> S save(S entity);

	/**
	 * Saves all given entities.
	 *
	 * @param entities must not be {@literal null}.
	 * @return the saved entities; will never be {@literal null}.
	 * @throws IllegalArgumentException in case the given entity is {@literal null}.
	 */
	<S extends T> Iterable<S> saveAll(Iterable<S> entities);

	/**
	 * Retrieves an entity by its id.
	 *
	 * @param id must not be {@literal null}.
	 * @return the entity with the given id or {@literal Optional#empty()} if none found
	 * @throws IllegalArgumentException if {@code id} is {@literal null}.
	 */
	Optional<T> findById(ID id);

	/**
	 * Returns whether an entity with the given id exists.
	 *
	 * @param id must not be {@literal null}.
	 * @return {@literal true} if an entity with the given id exists, {@literal false} otherwise.
	 * @throws IllegalArgumentException if {@code id} is {@literal null}.
	 */
	boolean existsById(ID id);

	/**
	 * Returns all instances of the type.
	 *
	 * @return all entities
	 */
	Iterable<T> findAll();

	/**
	 * Returns all instances of the type with the given IDs.
	 *
	 * @param ids
	 * @return
	 */
	Iterable<T> findAllById(Iterable<ID> ids);

	/**
	 * Returns the number of entities available.
	 *
	 * @return the number of entities
	 */
	long count();

	/**
	 * Deletes the entity with the given id.
	 *
	 * @param id must not be {@literal null}.
	 * @throws IllegalArgumentException in case the given {@code id} is {@literal null}
	 */
	void deleteById(ID id);

	/**
	 * Deletes a given entity.
	 *
	 * @param entity
	 * @throws IllegalArgumentException in case the given entity is {@literal null}.
	 */
	void delete(T entity);

	/**
	 * Deletes the given entities.
	 *
	 * @param entities
	 * @throws IllegalArgumentException in case the given {@link Iterable} is {@literal null}.
	 */
	void deleteAll(Iterable<? extends T> entities);

	/**
	 * Deletes all entities managed by the repository.
	 */
	void deleteAll();
}
```

Spring-data还针对持久化技术的不同，提供了特定的抽象接口，比如`JpaRepository`和`MongoRepository`。这些借口也都继承了`CrudRepository`，但是额外提供了其它特定的功能。

另外，在`CrudRepository`接口基础上，还有一个`PagingAndSortingRepository`借口抽象，其提供了分页和排序的功能。

```java
@NoRepositoryBean
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {

	/**
	 * Returns all entities sorted by the given options.
	 *
	 * @param sort
	 * @return all entities sorted by the given options
	 */
	Iterable<T> findAll(Sort sort);

	/**
	 * Returns a {@link Page} of entities meeting the paging restriction provided in the {@code Pageable} object.
	 *
	 * @param pageable
	 * @return a page of entities
	 */
	Page<T> findAll(Pageable pageable);
}
```

**对于大多数CRUD操作，无须编写任何代码，只要继承接口即可。**

### 定制化

- 可以通过在接口中增加函数来增加特定的功能，根据函数名即可定制化功能。
- 可以通过`@Query`注解，其是jpa的注解，可以使用sql或jpql定义。



## AOP

Spring AOP默认将标准JDK动态代理用于AOP代理。这使得可以代理任何接口（或一组接口）。 Spring AOP也可以使用CGLIB代理。默认情况下，如果业务对象未实现接口，则使用CGLIB。

> Spring AOP only supports method execution join points for Spring beans, so you can think of a pointcut as matching the execution of methods on Spring beans. 

### pointcut

> A pointcut declaration has two parts: a signature comprising a name and any parameters and a pointcut expression that determines exactly which method executions we are interested in. In the @AspectJ annotation-style of AOP, a pointcut signature is provided by a regular method definition, and the pointcut expression is indicated by using the `@Pointcut` annotation (the method serving as the pointcut signature must have a `void` return type).

pointcut翻译过来是切入点的意思，其表示我们感兴趣的joint point，也是我们植入advice的地方。

切入点表达式支持以下AspectJ pointcut designators (PCD)：

- `execution`: For matching method execution join points. This is the primary pointcut designator to use when working with Spring AOP.
- `within`: Limits matching to join points within certain types (the execution of a method declared within a matching type when using Spring AOP).
- `this`: Limits matching to join points (the execution of methods when using Spring AOP) where the bean reference (Spring AOP proxy) is an instance of the given type.
- `target`: Limits matching to join points (the execution of methods when using Spring AOP) where the target object (application object being proxied) is an instance of the given type.
- `args`: Limits matching to join points (the execution of methods when using Spring AOP) where the arguments are instances of the given types.
- `@target`: Limits matching to join points (the execution of methods when using Spring AOP) where **the class of the executing object has an annotation of the given type**.
- `@args`: Limits matching to join points (the execution of methods when using Spring AOP) where the runtime type of the actual arguments passed have annotations of the given types.
- `@within`: Limits matching to join points within types that have the given annotation (the execution of methods declared in types with the given annotation when using Spring AOP).

另外，切点表达式可以使用通配符：

- `*`匹配任何字符，但是限制一个
- `..`匹配任意字符，可以多个
- `+`跟在类名后面，匹配指定类及其继承类

## Druid连接池

[github](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)

## 缓存

[文档](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-caching)

spring boot会自动配置缓存，只要在入口处加上注解`@EnableCaching`。

### Cache vs Buffer

> The terms, “buffer” and “cache,” tend to be used interchangeably. Note, however, that they represent different things. Traditionally, a buffer is used as an intermediate temporary store for data between a fast and a slow entity. As one party would have to wait for the other (which affects performance), the buffer alleviates（缓解） this by allowing entire blocks of data to move at once rather than in small chunks. The data is written and read only once from the buffer. Furthermore, the buffers are visible to at least one party that is aware of it.
>
> A cache, on the other hand, is, by definition, hidden, and neither party is aware that caching occurs. It also improves performance but does so by letting the same data be read multiple times in a fast fashion.

两点区别：

- buffer作为一个临时存储，用来让高速和低速的组件能够互相高效获取数据，往往是写一大块数据。对于使用buffer的应用而言，其是可以感知的。
- cache是透明的，使用cache的应用并不知道是否使用了cache。它可以提升针对同一数据多次查询时的性能。

### 主要组件

| 名称           | 解释                                                         |
| :------------- | :----------------------------------------------------------- |
| Cache          | 缓存接口，定义缓存操作。实现有：RedisCache、EhCacheCache、ConcurrentMapCache等 |
| CacheManager   | 缓存管理器，管理各种缓存（cache）组件                        |
| @Cacheable     | 主要针对方法配置，能够根据方法的请求参数对其进行缓存。如果有就不调用方法，否则调用方法，然后更新缓存。 |
| @CacheEvict    | 清空缓存                                                     |
| @CachePut      | 保证方法被调用，又希望结果被缓存。 与@Cacheable区别在于是否每次都调用方法，常用于更新。 |
| @EnableCaching | 开启基于注解的缓存                                           |
| keyGenerator   | 缓存数据时key生成策略                                        |
| serialize      | 缓存数据时value序列化策略                                    |
| @CacheConfig   | 统一配置本类的缓存注解的属性                                 |

 **@Cacheable/@CachePut/@CacheEvict 主要的参数**

| 名称                           | 解释                                                         |
| :----------------------------- | :----------------------------------------------------------- |
| value                          | 缓存的名称，在 spring 配置文件中定义，必须指定至少一个 例如： @Cacheable(value=”mycache”) 或者 @Cacheable(value={”cache1”,”cache2”} |
| key                            | 缓存的 key，可以为空，如果指定要按照 SpEL 表达式编写， 如果不指定，则缺省按照方法的所有参数进行组合 例如： @Cacheable(value=”testcache”,key=”#id”) |
| condition                      | 缓存的条件，可以为空，使用 SpEL 编写，返回 true 或者 false， 只有为 true 才进行缓存/清除缓存 例如：@Cacheable(value=”testcache”,condition=”#userName.length()>2”) |
| unless                         | 否定缓存。当条件结果为TRUE时，就不会缓存。 @Cacheable(value=”testcache”,unless=”#userName.length()>2”) |
| allEntries (@CacheEvict )      | 是否清空所有缓存内容，缺省为 false，如果指定为 true， 则方法调用后将立即清空所有缓存 例如： @CachEvict(value=”testcache”,allEntries=true) |
| beforeInvocation (@CacheEvict) | 是否在方法执行前就清空，缺省为 false，如果指定为 true， 则在方法还没有执行的时候就清空缓存，缺省情况下，如果方法 执行抛出异常，则不会清空缓存 例如： @CachEvict(value=”testcache”，beforeInvocation=true) |

### 可用的SpEL

| 名称          | 位置       | 描述                                                         | 示例                   |
| :------------ | :--------- | :----------------------------------------------------------- | :--------------------- |
| methodName    | root对象   | 当前被调用的方法名                                           | `#root.methodname`     |
| method        | root对象   | 当前被调用的方法                                             | `#root.method.name`    |
| target        | root对象   | 当前被调用的目标对象实例                                     | `#root.target`         |
| targetClass   | root对象   | 当前被调用的目标对象的类                                     | `#root.targetClass`    |
| args          | root对象   | 当前被调用的方法的参数列表                                   | `#root.args[0]`        |
| caches        | root对象   | 当前方法调用使用的缓存列表                                   | `#root.caches[0].name` |
| Argument Name | 执行上下文 | 当前被调用的方法的参数，如findArtisan(Artisan artisan),可以通过#artsian.id获得参数 | `#artsian.id`          |
| result        | 执行上下文 | 方法执行后的返回值（仅当方法执行后的判断有效，如 unless cacheEvict的beforeInvocation=false） | `#result`              |

使用方法参数时我们可以直接使用“#参数名”或者“#p参数index”。

