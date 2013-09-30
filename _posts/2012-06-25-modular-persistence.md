---
layout: post
title: "Modular Persistence"

---
Hi everybody!

One of the classes of my post-graduate was the Java Persistence API ([JPA](http://jcp.org/en/jsr/detail?id=317)).

The teacher used the "normal" way to create the projects: Eclipse, create libraries with jars that's needed and create the EntityManagerFactory by hand.

It works, but IMHO, should never be used in production. In my examples and work, I used a standard architecture that I built. It's using Maven, JPA2, EclipseLink, and MySQL Google Guice.

This structure decreased a lot time I would take to do several things, and as I found it very useful, I decided to put it on [github](https://github.com/caarlos0/persistence-base).

To show hot it works, let's do a little example.

## Step-by-step tutorial

### Get the base code

First, clone the repo:

{% highlight bash %}
git clone https://github.com/caarlos0/persistence-base sample
{% endhighlight %}

### Do your hacks

Then, open the project in your preffered IDE, then, open the `pom.xml` file and change the project name to "sample". Save.

Open `src/main/java/com/github/caarlos0/model` and create a class called `Foo` with the following code:

{% highlight java %}
@Entity
public class Foo extends Bean {

  private String bar;

  public Foo() {
  }

  public Foo(Long id, Long version, String bar) {
    super(id, version);
    this.bar = bar;
  }

  public String getBar() {
    return bar;
  }

  public void setBar(String bar) {
    this.bar = bar;
  }
}
{% endhighlight %}

Now, we have to create the specific DAO for this entity. Go into `src/main/java/com/github/caarlos0/dao` and create a `FooDao.java`, with this code:

{% highlight java %}
public class FooDao extends AbstractDao<Foo> {
  @Inject
  public FooDao(Provider<EntityManager> emf) {
      super(emf, Foo.class);
  }
}
{% endhighlight %}

We also need to setup our `PersistenceModule` to bind this DAO. Open `src/main/java/com/github/caarlos0/dao/inject/PersistenceModule.java` adding the bind to `FooDao` to look like this:

{% highlight java %}
public class PersistenceModule extends AbstractModule {

  @Override
  protected void configure() {
    install(new JpaPersistModule("base")); // base has to be the PU in persistence.xml

    bind(PersistenceInitializer.class);

    bind(FooDao.class);
  }
}
{% endhighlight %}

If you want to change the _Persistence Unit_ name, you will have to do this in the `install(new JpaPersistModule("base"));` changing _base_ to the name that you want, put the same name in `src/main/resources/META-INF/persistence.xml`.

We have to add classes, configure the database and etc in the `src/main/resources/META-INF/persistence.xml` file:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.0" xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/persistence http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">
  <persistence-unit name="base" transaction-type="RESOURCE_LOCAL">
    <provider>org.eclipse.persistence.jpa.PersistenceProvider</provider>
    <class>com.github.caarlos0.model.Foo</class>
    <properties>
      <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/foodb"/>
      <property name="javax.persistence.jdbc.password" value=""/>
      <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
      <property name="javax.persistence.jdbc.user" value="root"/>
      <property name="eclipselink.ddl-generation" value="create-tables"/>
    </properties>
  </persistence-unit>
</persistence>
{% endhighlight %}

### Create the database

Now, we have to create the database in our MySQL:

{% highlight bash %}
mysqladmin -u root -p create foodb
{% endhighlight %}

### Test it

Now, let's write a test. Open `src/main/java/com/github/caarlos0/App.java` and do some code.

{% highlight java %}
public class App {
  public static void main(String[] args) {
    Injector i = PersistenceHelper.getInjector();

    FooDao dao = i.getInstance(FooDao.class);
    Foo foo = new Foo();
    foo.setBar("Beer!");
    dao.save(foo);
  }
}
{% endhighlight %}

Run the tests with

{% highlight sh%}
mvn exec:java -Dexec.mainClass="com.github.caarlos0.App" -Dexec.classpathScope=runtime
{% endhighlight %}

or direct in your IDE.

BOOM, it works :)

Good hack.

