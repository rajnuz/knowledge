# mongo 命令行

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [mongo 命令行](#mongo-命令行)
  - [Query cmd](#query-cmd)
    - [模糊查询](#模糊查询)

<!-- /code_chunk_output -->


## Query cmd

### 模糊查询

通过模糊查询查询一批符合条件的记录：

例如我希望查询mapper这个collection中，entityName字段为 AAA开头的记录

```sh
db.getCollection('mapper').find({"entityName":{$regex:/^AAA/}})
```

例如我希望查询mapper这个collection中，entityName字段为 AAA结尾的记录

```sh
db.getCollection('mapper').find({"entityName":{$regex:/AAA^/}})
```