`weixin_articles_and_weixiners`

`weixin_articles` 的下划线`id`修改为`weixin_articles_uid`

`weixiners` 的下划线`id` 修改为`weixiners_uid`

```
curl -XPUT http://localhost:9222/_template/weixin_articles_and_weixiners -d '
{
    "template": "weixin_articles_and_weixiners*",
    "settings": {
        "refresh_interval": "60s",
        "number_of_replicas": "1",
        "number_of_shards": "45"
    },
    "mappings": {
        "_default_": {
            "_all": {
                "enabled": true
            },
            "_source": {
                "enabled": true
            },
            "dynamic_templates": [
                {
                    "message_field": {
                        "match": "message",
                        "match_mapping_type": "string",
                        "mapping": {
                            "type": "text"
                        }
                    }
                },
                {
                    "string_fields": {
                        "match": "*",
                        "match_mapping_type": "string",
                        "mapping": {
                            "type": "keyword"
                        }
                    }
                },
                {
                    "double_fields": {
                        "match": "*",
                        "match_mapping_type": "double",
                        "mapping": {
                            "type": "double"
                        }
                    }
                },
                {
                    "byte_fields": {
                        "match": "*",
                        "match_mapping_type": "byte",
                        "mapping": {
                            "type": "byte"
                        }
                    }
                },
                {
                    "short_fields": {
                        "match": "*",
                        "match_mapping_type": "short",
                        "mapping": {
                            "type": "short"
                        }
                    }
                },
                {
                    "integer_fields": {
                        "match": "*",
                        "match_mapping_type": "integer",
                        "mapping": {
                            "type": "integer"
                        }
                    }
                },
                {
                    "long_fields": {
                        "match": "*",
                        "match_mapping_type": "long",
                        "mapping": {
                            "type": "long"
                        }
                    }
                },
                {
                    "date_fields": {
                        "match": "*",
                        "match_mapping_type": "date",
                        "mapping": {
                            "type": "date"
                        }
                    }
                },
                {
                    "geo_point_fields": {
                        "match": "*",
                        "match_mapping_type": "geo_point",
                        "mapping": {
                            "type": "geo_point"
                        }
                    }
                }
            ],
            "properties": {
                "@timestamp": {
                    "type": "date"
                },
                "score": {
                    "type": "float"
                },
                "weixiners_uid": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "openid": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "name": {
                    "type": "keyword",
                    "ignore_above": 256,
                    "fields": {
                        "raw": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_max_word"
                        }
                    }
                },
                "username": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "intro": {
                    "type": "text",
                    "analyzer": "ik_max_word",
                    "search_analyzer": "ik_max_word"
                },
                "certification": {
                    "type": "keyword",
                    "ignore_above": 256,
                    "fields": {
                        "raw": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_max_word"
                        }
                    }
                },
                "crawled_at": {
                    "format": "strict_date_optional_time||epoch_millis",
                    "type": "date"
                },
                "updated_at": {
                    "format": "strict_date_optional_time||epoch_millis",
                    "type": "date"
                },
                "tags": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "categories": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "copyright": {
                    "type": "boolean"
                },
                "stat_like_count": {
                    "type": "long"
                },
                "author": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "mid": {
                    "type": "long"
                },
                "stat_status": {
                    "type": "long"
                },
                "title": {
                    "type": "keyword",
                    "ignore_above": 256,
                    "fields": {
                        "raw": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_max_word"
                        }
                    }
                },
                "content": {
                    "type": "keyword",
                    "ignore_above": 256,
                    "fields": {
                        "raw": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_max_word"
                        }
                    }
                },
                "source_url": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "last_modified_at": {
                    "format": "strict_date_optional_time||epoch_millis",
                    "type": "date"
                },
                "cover": {
                    "type": "keyword",
                    "ignore_above": 256,
                    "fields": {
                        "raw": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_max_word"
                        }
                    }
                },
                "biz": {
                    "type": "keyword",
                    "ignore_above": 256,
                    "fields": {
                        "raw": {
                            "type": "text",
                            "analyzer": "ik_max_word",
                            "search_analyzer": "ik_max_word"
                        }
                    }
                },
                "stat_real_read_num": {
                    "type": "long"
                },
                "digest": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "stat_ret": {
                    "type": "long"
                },
                "stat_read_count": {
                    "type": "long"
                },
                "weixin_articles_uid": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "sn": {
                    "type": "keyword",
                    "ignore_above": 256
                },
                "idx": {
                    "type": "long"
                },
                "stat_info_crawled_at": {
                    "format": "strict_date_optional_time||epoch_millis",
                    "type": "date"
                },
                "stat_interval": {
                    "type": "long"
                },
                "fileid": {
                    "type": "long"
                }
            }
        }
    }
}'
```


