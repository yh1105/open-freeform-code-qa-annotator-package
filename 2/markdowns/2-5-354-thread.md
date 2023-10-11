
# Post \#52416970 [Link](https://stackoverflow.com/questions/52416970/)

## OneToMany & ManyToOne mapping JPA / Hibernate

**Vote**: 13 (343/702) **Views**: 100385 (86/702) 

**Internal ID** \#2-5-354

Created at 2018-09-20 03:05:33

Tags: `hibernate` `jpa` `spring-data-jpa` `one-to-many` `many-to-one`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I have two tables with foreign key relations. I've tried searching on how to do it and it always leads to OneToMany & ManyToOne mapping. I have these two tables.

user_role

[](https://i.stack.imgur.com/VbiD7.png)

user

[](https://i.stack.imgur.com/4PV7N.png)

I'm trying to display all the users and show their position by getting the value of the position column to the user_role table.

These are my classes

User.java

```
@Entity
@Table(name="user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    ..........

    private UserRole userRole;

    @ManyToOne()
    @JoinColumn(name="role_id")
    public UserRole getUserRole() {
        return userRole;
    }

    public void setUserRole(UserRole userRole) {
        this.userRole = userRole;
    }

    .......
}
```


UserRole.java

```
@Entity
@Table(name="user_role")
public class UserRole {

    .......

    private List<User> user;

    @OneToMany(targetEntity=User.class, mappedBy="userRole",cascade=CascadeType.ALL, fetch = FetchType.LAZY)
    public List<User> getUser() {
        return user;
    }

    public void setUser(List<User> user) {
        this.user = user;
    }

    public long getRole_id() {
        return role_id;
    }

    public void setRole_id(long role_id) {
        this.role_id = role_id;
    }

    .........

}
```


And I keep getting this error.

```
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Invocation of init method failed; nested exception is javax.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.MappingException: Could not determine type for: java.util.List, at table: user_role, for columns: [org.hibernate.mapping.Column(user)]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1699) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:573) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:495) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:317) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:222) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:315) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:199) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1089) ~[spring-context-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:859) ~[spring-context-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:550) ~[spring-context-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:140) ~[spring-boot-2.0.5.RELEASE.jar:2.0.5.RELEASE]
    at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:780) [spring-boot-2.0.5.RELEASE.jar:2.0.5.RELEASE]
    at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:412) [spring-boot-2.0.5.RELEASE.jar:2.0.5.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:333) [spring-boot-2.0.5.RELEASE.jar:2.0.5.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1277) [spring-boot-2.0.5.RELEASE.jar:2.0.5.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1265) [spring-boot-2.0.5.RELEASE.jar:2.0.5.RELEASE]
    at com.rtc_insurance.AdminApplication.main(AdminApplication.java:10) [classes/:na]
Caused by: javax.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.MappingException: Could not determine type for: java.util.List, at table: user_role, for columns: [org.hibernate.mapping.Column(user)]
    at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:402) ~[spring-orm-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:377) ~[spring-orm-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:341) ~[spring-orm-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1758) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1695) ~[spring-beans-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    ... 16 common frames omitted
Caused by: org.hibernate.MappingException: Could not determine type for: java.util.List, at table: user_role, for columns: [org.hibernate.mapping.Column(user)]
    at org.hibernate.mapping.SimpleValue.getType(SimpleValue.java:456) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.mapping.SimpleValue.isValid(SimpleValue.java:423) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.mapping.Property.isValid(Property.java:226) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.mapping.PersistentClass.validate(PersistentClass.java:597) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.mapping.RootClass.validate(RootClass.java:265) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.boot.internal.MetadataImpl.validate(MetadataImpl.java:329) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.boot.internal.SessionFactoryBuilderImpl.build(SessionFactoryBuilderImpl.java:461) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:892) ~[hibernate-core-5.2.17.Final.jar:5.2.17.Final]
    at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:57) ~[spring-orm-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:365) ~[spring-orm-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:390) ~[spring-orm-5.0.9.RELEASE.jar:5.0.9.RELEASE]
    ... 20 common frames omitted
```


It's my first time using spring so I'm not that familiar. Any help would be greatly appreciated. 


----------
        
## Answer \#0

**Accepted** Vote: 14

Created at 2018-09-20 03:40:52

------------

Can you move the relationship to a related object like this

```
@ManyToOne()
@JoinColumn(name="role_id", referencedColumnName = "role_id", insertable = false, updatable = false)    
private UserRole userRole;
```


and same do for userRole

```
@OneToMany(targetEntity=User.class, mappedBy="userRole",cascade=CascadeType.ALL, fetch = FetchType.LAZY)    
private List<User> user = new ArrayList<>();
```




The jar file seems to be corrupted. Try removing the content from the .m2\repository and `mvn clean install`

OR 

Right-click your project, select Maven, Update Project, check on Force Update of Snapshots/Releases.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2019-03-11 08:33:58

------------

Just a hint, check your entity classes and ensure you have included @Entity annotation just before the class declaration statement.

```
@Entity
public class MyEntityClass{
 //code
}
```


Then on any other entity class that uses MyEntityClass above under the @ManyToMany or @OneToMany relationship add the following.

```
@OneToMany(mappedBy="mappingItem", cascade=CascadeType.ALL, orphanRemoval=true)
@JsonIgnore
private List<MyEntityClass> myEntityClassItemList;
```


This approach worked for me some how, may be it might help.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2018-09-20 03:31:51

------------

You have to annotate the Java fields not the getter like:

```
@OneToMany(targetEntity=User.class, mappedBy="userRole",cascade=CascadeType.ALL, fetch = FetchType.LAZY)
private List<User> user;
```


and for `User.class`

```
@ManyToOne
@JoinColumn(name="role_id")
private UserRole userRole;
```


In addition, do you have defined the `role_id` column in the `user` table? I can't see this in the screenshot


------------
    
    
## Answer \#3

 Vote: 2

Created at 2018-09-20 05:17:01

------------

I can re-create your exception by mixing my annotation mapping between fields and properties. 
I see in the problem you described above you annotate the field Id, but then annotate the property userRole, if I do the same I get the same error.
I can fix the error by either annotating just properties or fields.
Both these models work:

```
@Entity
@Table(name = "USER")
public class User {


private Long id;
private UserRole userRole;
String userName;

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
public Long getId() {
    return id;
}


public void setId(Long id) {
    this.id = id;
}

@Column(name = "USER_NAME")
public String getUserName() {
    return userName;
}

public void setUserName(String userName) {
    this.userName = userName;
}

@ManyToOne()
@JoinColumn(name="role_id")
public UserRole getUserRole() {
    return userRole;
}

public void setUserRole(UserRole userRole) {
    this.userRole = userRole;
}
}
```


This also works:

```
@Entity
@Table(name = "USER")
public class User {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;


@ManyToOne()
@JoinColumn(name="role_id")
private UserRole userRole;


@Column(name = "USER_NAME")
String userName;

public Long getId() {
    return id;
}

public void setId(Long id) {
    this.id = id;
}

public String getUserName() {
    return userName;
}

public void setUserName(String userName) {
    this.userName = userName;
}

public UserRole getUserRole() {
    return userRole;
}

public void setUserRole(UserRole userRole) {
    this.userRole = userRole;
}
}
```


Example of the other model object annotating the fields

```
@Entity
@Table(name = "USER")
public class User {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;


@ManyToOne()
@JoinColumn(name="role_id")
private UserRole userRole;


@Column(name = "USER_NAME")
String userName;

public Long getId() {
    return id;
}

public void setId(Long id) {
    this.id = id;
}

public String getUserName() {
    return userName;
}

public void setUserName(String userName) {
    this.userName = userName;
}

public UserRole getUserRole() {
    return userRole;
}

public void setUserRole(UserRole userRole) {
    this.userRole = userRole;
}
}
```


Test Code (I added this because there is often confusion that one must set the bidirectional relationship in order to get both users and userRole to persist with a save to the UserRole).

```
List<User> users = new ArrayList<>();

    User user1 = new User();
    user1.setUserName("user1");
    users.add(user1);

    User user2 = new User();
    user2.setUserName("user2");
    users.add(user2);

    UserRole userRole = new UserRole();
    userRole.setRoleName("admin");
    //Unidirectional relationship
    user1.setUserRole(userRole);
    user2.setUserRole(userRole);
    //set Bidirectional relationship
    userRole.setUsers(users);

    userRole = userRoleRepository.save(userRole);

    //Show that the two users and the UserRole persisted
    UserRole result = userRoleRepository.findById(userRole.getId()).get();
    assertEquals(2, result.getUsers().size());
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-07-15 17:36:11

------------

you can write one to many relations like this:
position -foreign key
```
public class UserRole {
.....
@OneToMany(targetEntity = User.class,cascade =CascadeType.ALL)
@JoinColumn(name="position",referencedColumnName = "role_id")
private List<User> users; 
......  
}
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-09-16 08:13:41

------------

A simple and another approach to create and map the users and their roles.
Define two entities class along with relation and it will create the third mapping table :

```
@Entity
    @Table(name = "ROLE")
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Role {
    
        @Id
        @GeneratedValue(strategy = GenerationType.AUTO)
        private long uuid;
        .
        .
        .
        
    }
```


```
@Entity
 @Table(name = "USER")
 @Data
 @AllArgsConstructor
 @NoArgsConstructor
 public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long uuid;
    .
    .
    .
    @ManyToMany(cascade=CascadeType.ALL,fetch=FetchType.EAGER)
        @JoinTable(name="user_roles",
            joinColumns = {@JoinColumn(name="user_id", referencedColumnName="id")},
            inverseJoinColumns = {@JoinColumn(name="role_id", referencedColumnName="id")}
        )
    private List<Role> roles;
    
    
}
```

Above code generate three table :

1. ROLE 2. USER 3. USER_ROLE




------------
    
    