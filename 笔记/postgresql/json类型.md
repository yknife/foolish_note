1. json的包含关系

   ```sql
   -- Simple scalar/primitive values contain only the identical value:
   SELECT '"foo"'::jsonb @> '"foo"'::jsonb;
   
   -- The array on the right side is contained within the one on the left:
   SELECT '[1, 2, 3]'::jsonb @> '[1, 3]'::jsonb;
   
   -- Order of array elements is not significant, so this is also true:
   SELECT '[1, 2, 3]'::jsonb @> '[3, 1]'::jsonb;
   
   -- Duplicate array elements don't matter either:
   SELECT '[1, 2, 3]'::jsonb @> '[1, 2, 2]'::jsonb;
   
   -- The object with a single pair on the right side is contained
   -- within the object on the left side:
   SELECT '{"product": "PostgreSQL", "version": 9.4, "jsonb": true}'::jsonb @> '{"version": 9.4}'::jsonb;
   
   -- The array on the right side is not considered contained within the
   -- array on the left, even though a similar array is nested within it:
   SELECT '[1, 2, [1, 3]]'::jsonb @> '[1, 3]'::jsonb;  -- yields false
   
   -- But with a layer of nesting, it is contained:
   SELECT '[1, 2, [1, 3]]'::jsonb @> '[[1, 3]]'::jsonb;
   
   -- Similarly, containment is not reported here:
   SELECT '{"foo": {"bar": "baz"}}'::jsonb @> '{"bar": "baz"}'::jsonb;  -- yields false
   
   -- A top-level key and an empty object is contained:
   SELECT '{"foo": {"bar": "baz"}}'::jsonb @> '{"foo": {}}'::jsonb;
   
   -- This array contains the primitive string value:
   SELECT '["foo", "bar"]'::jsonb @> '"bar"'::jsonb;
   
   -- This exception is not reciprocal -- non-containment is reported here:
   SELECT '"bar"'::jsonb @> '["bar"]'::jsonb;  -- yields false
   ```

2. json的存在关系

   ```sql
   -- String exists as array element:
   SELECT '["foo", "bar", "baz"]'::jsonb ? 'bar';
   
   -- String exists as object key:
   SELECT '{"foo": "bar"}'::jsonb ? 'foo';
   
   -- Object values are not considered:
   SELECT '{"foo": "bar"}'::jsonb ? 'bar';  -- yields false
   
   -- As with containment, existence must match at the top level:
   SELECT '{"foo": {"bar": "baz"}}'::jsonb ? 'bar'; -- yields false
   
   -- A string is considered to exist if it matches a primitive JSON string:
   SELECT '"foo"'::jsonb ? 'foo';
   ```

3. 查询json，需要先建立索引，默认索引支持`?`, `?&` and `?|`  `@>`

   ```sql
   CREATE INDEX idxgin ON api USING GIN (jdoc);
   ```

   jsonb_path_ops索引只支持 `@>` 

   ```sql
   CREATE INDEX idxginp ON api USING GIN (jdoc jsonb_path_ops);
   ```

   准备数据

   ```json
   {
       "guid": "9c36adc1-7fb5-4d5b-83b4-90356a46061a",
       "name": "Angela Barton",
       "is_active": true,
       "company": "Magnafone",
       "address": "178 Howard Place, Gulf, Washington, 702",
       "registered": "2009-11-07T08:53:22 +08:00",
       "latitude": 19.793713,
       "longitude": 86.513373,
       "tags": [
           "enim",
           "aliquip",
           "qui"
       ]
   }
   ```

   ```sql
   -- Find documents in which the key "company" has value "Magnafone"
   SELECT jdoc->'guid', jdoc->'name' FROM api WHERE jdoc @> '{"company": "Magnafone"}';
   ```