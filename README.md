# JSON support for Mybatis 3.x using Gson 2.x

Provide support for JSON like field types in any Database.
I'm developed this handler with PostgreSql in mind, 
but it looks like it can be used with any other database even without JSON support.

Artifact does not include direct gson dependencies - it is up to you to add them into your project.
Looks like you can use any Gson version compatible by API with 2.6.x.

##How does it work
Because JDBC does not support JSON types, it transfer JSON to/from database as a string.
We searialize JSON to string when we are saving and deserialize from string when reading.
This feature means that we are really do not care if our DB can support JSON or not.

###Keep in mind
You should mind that you should use your DB API to convert JSON string comming from MyBatis 
to appropriate JSON representation into DB while inserting/updating fields.
Look into tests for some PostgreSql examples.

###Lazy reading
During reading from DB handler return TreeNode wrapper that actually does not parse JSON from string.
It is waiting for you to call any of its methods - only then it will read JSON into structure.
But this approach may lead to unexpected runtime exception in a case if your database will return
invalid JSON string.


## Adding to your project

For now this package is available only via https://jitpack.io/

I'm expecting that you are already have Gson dependencies in your project.
```compile 'com.google.code.gson:gson:2.+''```

### Gradle dependencies
```
repositories {
  maven {
    url "https://jitpack.io"
  }
}

dependencies {
  compile 'com.github.javaplugs:mybatis-gson:0.1'
}
```

### Maven dependencies
```
<repository>
    <id>jitpack.io</id>
    <url>https://jitpack.io</url>
</repository>

<dependency>
    <groupId>com.github.javaplugs</groupId>
    <artifactId>mybatis-gson</artifactId>
    <version>0.1</version>
</dependency>
```

## Configure
In result map configuration you should use ```javaType=com.google.gson.JsonElement"```

You should not configure anything if you want to use JsonElement types as arguments in your mapper
functions, but keep in mind that handler only expect objects of type JsonArray or JsonObject.

### Mybatis config
```
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="com.github.javaplugs.mybatis.JsonElementTypeHandler"/>
</typeHandlers>
```

Or you can use package search

```
<!-- mybatis-config.xml -->
<typeHandlers>
  <package name="com.github.javaplugs.mybatis"/>
</typeHandlers>
```

### Mybatis via Spring
```
<bean id="SomeId" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!-- your configuration -->
    <property name="typeHandlersPackage" value="com.github.javaplugs.mybatis" />
</bean>
```