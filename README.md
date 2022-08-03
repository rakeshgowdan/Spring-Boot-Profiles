# Spring-Boot-Profiles

![alt text](https://miro.medium.com/max/1400/1*9CByRn_TuEzEqTvF0gAEhw.png)


## What are Profiles?
Spring framework provides the ability to group configuration properties into profiles, allowing us to activate them depending on the environment we deploy. Simply profile means a set of configurations.

## Real Need
Applications are typically deployed to environments like DEV, STAGE(QA), and PROD. In accordance with the deploying environment, several things change. As an example, we can take database URL. We define this URL inside application.properties OR application.yml while we develop changes locally.  Based on it we can connect to relavent database.

```

// STAGE ENV URL
spring.datasource.url=jdbc:mysql://stage.db.server:3308/jpa-db
spring.jpa.generate-ddl=true
spring.jpa.show-sql=true
// PROD ENV URL
spring.datasource.url=jdbc:mysql://prod.db.server:3309/jpa-db
spring.jpa.generate-ddl=false
spring.jpa.show-sql=false
// DEV ENV URL
spring.datasource.url=jdbc:mysql://localhost:3306/jpa-db
spring.jpa.generate-ddl=true
spring.jpa.show-sql=true

```

The above setup shows that the URL and port can be changed. So, we cannot change this time to time and test.

By default, Spring Boot takes application file URL and start the app. If you see the startup logs closely, You will see this log.

```
No active profile set, falling back to 1 default profile: "default".

```

Using profiles, we can define set of profiles per env, and bind any env to application using a configuration! No need to change the already defined configurations until any URL or any other is getting changed.

## Setup Profiles
We have to define env names first. For this, I will consider profile names as dev, stage and prod.

So we should create 3 properties or yml files for each env. Be careful about the naming conventions! The name must be application-env.properties or yml.\
```
application.properties / yml
application-dev.properties / yml
application-stage.properties / yml
application-prod.properties / yml
```

Keep the default profile also. We need that to bind the active profile and to load common/unchanged configurations for all environments. These common configs will be same and constant in every env. Example is shown below. We can place such kind of configs in application.properties file.
```
spring.application.name=order-service
```

Let’s see how to bind active profile. Place the below line in application.properties file.

```
spring.profiles.active=stage
```

Now restart the application. See the startup logs…Did you notice a change? You should see this log now.
```
The following 1 profile is active: "stage"
```
Now database connection will be created using the URL and port included in application-stage.properties file.

## IMPORTANT NOTE 
But do not bind like this in production code branch, inside your default application.properties file. Do this only for local dev testing with any of the environments. Deployment scripts(DevOps side) will be responsible to deploy your code into the environments with relevant config file.

If you really need to maintain active profile in the code itself, you can go for env based code branches. The only change of these branches will be this.
```
spring.profiles.active=stage/dev/prod
```

For prod env, you can reserve master branch. Then all other env branches are having 1 commit ahead of master which contains the active profile change commit. Remember to rebase env branches regularly with the latest changes in master branch.

