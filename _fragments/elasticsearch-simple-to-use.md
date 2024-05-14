---
layout: fragment
title: Elasticsearch简单使用
tags: [es]
description: Elasticsearch简单使用
keywords: es
---





**Match Query（分词查询）**：查询用于搜索包含指定词汇的文档，根据类型分词或者不分词。

```php
GET /_search
{
  "query": {
    "match": {
      "field_name": "text to match"
    }
  }
}
```



**Term Query（精确查询）**：用于精确匹配字段的值。

```php
GET /_search
{
  "query": {
    "term": {
      "field_name": "exact_value"
    }
  }
}
```



**Range Query（范围查询）**：用于匹配字段的范围，比如日期范围或数字范围。

```php
GET /_search
{
  "query": {
    "range": {
      "field_name": {
        "gte": "start_value",
        "lte": "end_value"
      }
    }
  }
}
```



**Bool Query（布尔查询）**：支持 must（相当于AND）、must_not（相当于NOT）、should（相当于OR） 三种逻辑操作。

```php
GET /_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "field_name": "text to match"
          }
        },
        {
          "term": {
            "field_name": "value to exclude"
          }
        }
      ]
    }
  }
}
```



**Exists Query（存在查询）**：匹配包含指定字段的文档。

```php
GET /_search
{
  "query": {
    "exists": {
      "field": "field_name"
    }
  }
}
```



**Wildcard Query（通配符查询）**：(?)代表一个字符，使用星号(*)代表零个或多个字符。

```php
GET /_search
{
  "query": {
    "wildcard": {
      "field_name": "wildcard_pattern"
    }
  }
}
```



**Fuzzy Query（模糊查询）**：可以允许纠正错误拼写的个数。

```php
{
  "fuzzy": {
    "field_name": {
      "value": "search_term",
      "fuzziness": "AUTO"
    }
  }
}
```
