---
layout: post
title:  "Item batch update"
date:   2019-06-06 13:03:16 +0200
categories:
---
  Let's assume situation that we need to do update a lot of items during one http request.
But if there is no such item in a table we need to create it. With postgres help we can do it very simple.
For this we use postgres construction "ON CONFLICT .. DO UPDATE". That means if we already have in our table
item with the same name we update description column value for it in other case we create new item.
  Also we use batchUpdate method from JdbcTemplate class for doing batch update a lot of items together.
Below you can see example of this implementation:

{% highlight java %}
@Service
public class ItemBatchUpdateService {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    private static final String QUERY = "INSERT INTO public.item\n" +
            "(name, description)\n" +
            "VALUES(?, ?)\n" +
            "ON CONFLICT (name) \n" +
            "DO\n" +
            " UPDATE\n" +
            "   SET description = ?;";

    public void insertOrUpdate(List<Item> items) {
        jdbcTemplate.batchUpdate(QUERY,
                new BatchPreparedStatementSetter() {
                    @Override
                    public void setValues(Item item, int i) throws SQLException {
                        ps.setString(1, item.get(i).getName());
                        ps.setString(2, item.get(i).getDescription());
                        ps.setString(3, item.get(i).getDescription());
                    }

                    @Override
                    public int getBatchSize() {
                        return items.size();
                    }
                });
    }
}
{% endhighlight %}

Check out the [Postgres docs][postgres-docs] and [Spring docs][spring-docs] for more info.

[postgres-docs]: https://www.postgresql.org/docs/9.5/sql-insert.html
[spring-docs]:   https://docs.spring.io/spring/docs/3.0.0.M4/reference/html/ch12s04.html
