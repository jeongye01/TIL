[대장균의 크기에 따라 분류하기 2](https://school.programmers.co.kr/learn/courses/30/lessons/301649)
대장균의 크기 그룹을 NTILE 문법을 이용해 분류하고 분류한 결과를 토대로 COLONY_NAME을 부여

```
WITH Percentage AS
(
    SELECT 
        ID, 
        NTILE(4) OVER (ORDER BY size_of_colony DESC) AS size_group
    FROM 
        ECOLI_DATA
),
colonynames as (
   select id, 
          case
               when size_group = 1 then 'CRITICAL'
               when size_group = 2 then 'HIGH'
               when size_group = 3 then 'MEDIUM'
               when size_group = 4 then 'LOW'
         end as colony_name
    from percentage
)
select id,colony_name from colonynames order by id;

```
