# superset 缓存

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [superset 缓存](#superset-缓存)

<!-- /code_chunk_output -->


参考链接：
[1](https://superset.apache.org/docs/installation/cache/)
[2](https://www.restack.io/docs/superset-knowledge-superset-redis-auth-guide)

superset配置（helm chart）

superset cache配置

```yaml
configOverrides: 
  cache_config: |
    FILTER_STATE_CACHE_CONFIG = {
      'CACHE_TYPE': 'RedisCache',
      'CACHE_DEFAULT_TIMEOUT': 86400, # 1 day
      'CACHE_KEY_PREFIX': 'superset_filter_cache',
      'CACHE_REDIS_URL': 'redis://xxx:xxx@redis-master:6379/2'
    }
    EXPLORE_FORM_DATA_CACHE_CONFIG = {
      'CACHE_TYPE': 'RedisCache',
      'CACHE_DEFAULT_TIMEOUT': 86400, # 1 day
      'CACHE_KEY_PREFIX': 'superset_explore_cache',
      'CACHE_REDIS_URL': 'redis://:xxx:xxx@redis-master:6379/3'
    }
```

superset 有4个cache配置：
Dashboard filter state (required): `FILTER_STATE_CACHE_CONFIG`.
Explore chart form data (required): `EXPLORE_FORM_DATA_CACHE_CONFIG`
Metadata cache (optional): `CACHE_CONFIG`
Charting data queried from datasets (optional): `DATA_CACHE_CONFIG`

redis url的格式为 `redis://{username}:{password}@{host}:{port}/{db}`

除此之外还需要为每个在图表中为每个chart配置缓存过期时间。

![picture 0](asset_IMG/superset_cache/IMG_20240121-160314970.png)  

在dashboard强制刷新，或者设置自动刷新，会清除缓存重新查询。重启superset不影响redis cache生命周期
