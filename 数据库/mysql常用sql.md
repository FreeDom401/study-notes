1.查找表中多余的重复记录（多个字段）

```mysql
select * from t_userlogin a
where (a.username,a.phone) in (select username,phone from t_userlogin group by username,phone having count (*) > 1)
```

2.删除表中多余的重复记录（多个字段），只留有id最小的记录

```mysql
DELETE
 FROM    t_score 
WHERE
    (xn, xq,xh,kcmc) IN ( SELECT * FROM (
        SELECT
            xn,xq,xh,kcmc
        FROM
            t_score
        GROUP BY
            xn,xq,xh,kcmc
        HAVING
            count(*) > 1
    ) a)
AND id NOT IN (SELECT * FROM (
    SELECT
        min(id)
    FROM
        t_score
    GROUP BY
        xn,xq,xh,kcmc
    HAVING
        count(*) > 1
)b)
```

3.插入其他表中不重复的数据

```mysql
insert into t_student_sos(username) select username from t_student_basic where username not in (select username from t_student_sos);
```

4.查询多种状态下的sql(case  when---相当于if  else 判断)

```		  mysql
SELECT id,ORDER_ID,
          COUNT(CASE STATUS WHEN 'F' THEN TRUE END ) AS '已发送报告' ,
          COUNT(CASE STATUS WHEN 'D' THEN TRUE END ) AS '已生成报告',
          COUNT(CASE STATUS WHEN 'T' THEN TRUE END ) AS '未生成报告',
          MAX(SERVICE_CNT) AS '共计服务次数'
       FROM APP_NUTRITION_SERVICE_DETAIL WHERE  DELETE_MARK='N' 
```

