# Sqlserver 常用sql命令

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Sqlserver 常用sql命令](#sqlserver-常用sql命令)
  - [查询某个表的元数据](#查询某个表的元数据)

<!-- /code_chunk_output -->


## 查询某个表的元数据

```sql
SELECT 
    t.name AS 'Table Name', 
    c.name AS 'Column Name', 
    ty.name AS 'Data Type'
FROM 
    sys.views t
INNER JOIN 
    sys.columns c ON t.object_id = c.object_id
INNER JOIN 
    sys.types ty ON c.user_type_id = ty.user_type_id
WHERE 
    t.name = 'mytable'; -- 替换 YourTableName 为您的表名
```