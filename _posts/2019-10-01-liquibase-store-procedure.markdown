---
layout: post
title:  "Using storing procedure tag for liquibase "
date:   2019-10-01 10:23:16 +0200
categories:
---
Using storing procedure tag for liquibase

{% highlight xml %}
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog/1.9"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog/1.9
                      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-1.9.xsd">
    <changeSet id="2019.10.01" author="me" context="my-webservice">
        <sql>
            CREATE TABLE public.years (
            year integer NOT NULL,
            name varchar NOT NULL
            );
        </sql>
        <createProcedure>
                do $do$
                  begin
                     for y in 1970..2150 loop insert into public.years (year, season_id) values (y,1), (y,2);
                  end loop;
                end $do$;
         </createProcedure>
    </changeSet>
</databaseChangeLog>

{% endhighlight %}
