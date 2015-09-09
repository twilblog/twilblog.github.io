---
layout: post
title:  "Apache Camel with Spring Java Config"
date:   2015-07-27 15:00:00
author: andy
categories: java spring camel
---

Trying to get Apache Camel and Spring Java Config working together was not as easy as I thought it would be
considering there are [examples here](https://github.com/apache/camel/tree/master/examples/camel-example-spring-javaconfig)
and [documentation here](http://camel.apache.org/spring-java-config-example.html). I could not get the example
to work the way I expected it to and it all felt a bit too auto-magical to me with no explicit link between camel
and spring configuration.

So this is how I did it:

## 1. Add the dependencies:

{% highlight xml %}
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-spring</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-spring-javaconfig</artifactId>
</dependency>
{% endhighlight %}

## 2. Create the Java configuration class and set it on the Main

{% highlight java %}
public class MainApp {

    /**
     * A main() so we can easily run these routing rules in our IDE
     */
    public static void main(String... args) throws Exception {
        Main main = new Main();
        main.setConfigClassesString(RouteConfig.class.getName());
        main.run(args);
    }

    @Configuration
    static class RouteConfig extends SingleRouteCamelConfiguration {

        @Bean
        public PropertiesComponent properties() {
            PropertiesComponent pc = new PropertiesComponent();
            pc.setLocation("classpath:/app.properties");
            return pc;
        }

        @Override
        public RouteBuilder route() {
            return new RouteBuilder() {
                public void configure() {
                    from("direct:start").toF("log:%s?level=DEBUG", getClass().getName());
                }
            };
        }
    }
}
{% endhighlight %}

***NOTE:*** The Main class used is org.apache.camel.spring.javaconfig.Main (not the basic camel one) and also the
PropertiesComponent is not required but allows you to externalise your properties values to a file.

## Testing

Again, there were [many existing examples](https://camel.apache.org/spring-testing.html), but none that played well with 
me. 
However, this (very contrived) example shows what I ended up using to get the test working using the Java config:

{% highlight java %}

public class PixelCollectorTest extends CamelSpringTestSupport {

    @Produce(uri = "direct:start")
    protected ProducerTemplate template;

    @Test
    public void test() {
        Exchange request = createExchangeWithBody("test body");
        Exchange response = template.send(request);
        assertEquals("test body", response.getIn().getBody());
    }

    @Override
    protected AbstractApplicationContext createApplicationContext() {
        return new AnnotationConfigApplicationContext(RouteConfig.class);
    }
}

{% endhighlight %}
