科技资讯（索引名称、分片名称）：`tech_news`、[分片参考](/ying-she-yu-mo-ban/ke-ji-zi-xun-zhan-shu-ju.md)（`tech_36kr_articles`、`tech_q36kr_articles`、`tech_huxiu_articles`、`tech_leiphone_articles`、`tech_cyzone_articles`、`tech_tmtpost_articles`、`tech_geekpark_articles`、`tech_ikanchai_articles`、`tech_qianzhan_articles`、`tech_techxue_articles`、`tech_pingwest_articles`）

**POST 请求写入数据到 **`kafka`

`POST` `http://127.0.0.1/stq/api/v1/pa/kejizixun/add`

`HEADERS`：`"Content-Type" => "application/json"`

`BODY` 体参数说明：List 集合、数组

```
[
    {
      "id": "10",
      "index_name": "tech_news",
      "type_name": "tech_36kr_articles",
      "avatar_img": "http://p3.pstatp.com/thumb/6427/6955928713",
      "name": "乒乓网1",
      ...
    },
    {
      "id": "11",
      "index_name": "tech_news",
      "type_name": "tech_36kr_articles",
      "avatar_img": "http://p3.pstatp.com/thumb/6427/6955928713",
      "name": "乒乓网2",
      ...
    }
]
```

`logstash`** 消费**`kafka`**中的数据到 **`elasticsearch`

```
...
```

从 `kafka` 队列中读取的数据，两条符合预期（`index_name`、`type_name`、`id`会在`logstash filter`中处理后移除，保存或者更新数据到 `es`）

```
{
    "avatar_img" => "http://p3.pstatp.com/thumb/6427/6955928713",
          "name" => "乒乓网1"
}
{
    "avatar_img" => "http://p3.pstatp.com/thumb/6427/6955928713",
          "name" => "乒乓网2"
}
```


