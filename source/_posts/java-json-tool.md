---
title: use simple-json with java on example
date: 2020-08-13 14:36:29
tags:
---


##Simple json tool

JSONparser is used to convert string to json object


```        JSONParser parser = new JSONParser();

        Object obj = null;
        try {

            obj = parser.parse(new FileReader("C:/Users/Envy/Documents/company.json"));

        } catch (Exception e) {
            // Handle possible exceptions
            e.printStackTrace();
        }

        // If the parsed string was a single json object
        if (obj instanceof JSONObject) {
            JSONObject jsonObject = (JSONObject) obj;

            // Do something with jsonObject, in this example it prints the content of jsonObject
            System.out.println(jsonObject);

        }
        // Or else if the parsed string was a json array
        else if (obj instanceof JSONArray) {
            JSONArray jsonArray = (JSONArray) obj;

            // Do something with jsonArray, in this example it prints the content of jsonArray
            System.out.println(jsonArray);
        }
```


convert the JASONObject to string 

```java

        JSONObject obj = new JSONObject();
        obj.put("name","foo");
        obj.put("num",100);
        obj.put("balance",1000);
        obj.put("is_vip",true);
        
        StringWriter out = new StringWriter();
        try {
            obj.writeJSONString(out);
        }catch(IOException e ){
            System.out.println(e);
        }
```


##Spring boot framework -json support

references
http://json-b.net/docs/user-guide.html


spring boot support three different json libs

```
Spring Boot provides integration with three JSON mapping libraries:

Gson
Jackson
JSON-B
Jackson is the preferred and default library.

spring.http.converters.preferred-json-mapper=jsonb
```


```java
@Bean
public Jackson2ObjectMapperBuilderCustomizer customJson()
{
    return builder -> {

        builder.indentOutput(true);
        builder.propertyNamingStrategy(PropertyNamingStrategy.SNAKE_CASE);
    };
}
```


