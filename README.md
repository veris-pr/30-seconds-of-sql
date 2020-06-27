# 30-seconds-of-sql
> Inspired by the repo - [30-seconds/30-seconds-of-python](https://github.com/30-seconds/30-seconds-of-python)
> Short SQL snippets for common SQL problems, primary DB - PostgreSQL

# Problems

<details> 
  <summary>Extracting hierarchical objects</summary>
  
  http://sqlfiddle.com/#!17/46596/1
  
  Table name - Hierarchy
  
  | id (pk) | parent_id (self FK) | name (text) | data (jsonb)  |
  |---------|---------------------|-------------|---------------|
  | 1       | NULL                | first       | '{"a":1}' |
  | 2       | 1                   | second      | '{"b":2}' |
  | 3       | 2                   | third       | '{"c":3}' |
  | 4       | 3                   | fourth      | '{"d":4}' |
  ```sql
  WITH RECURSIVE H(id, array_tree_id, level, merged_data, parent)
      AS
      (
        SELECT id, ARRAY[id], 0, data, parent_id
        FROM Hierarchy
        WHERE parent_id is null


        UNION ALL

        SELECT C.id, ARRAY_APPEND(D.array_tree_id, C.id), D.level + 1, D.merged_data || C.data, C.parent_id
        FROM Hierarchy AS C
        INNER JOIN H AS D ON (D.id = C.parent_id)

      )
  SELECT id AS id, array_tree_id as Tree, level as Level, merged_data AS Data, parent from H
  ```
</details>
