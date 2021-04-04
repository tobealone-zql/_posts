---
title: Mysql-50道练习题
date: 2021-03-08 15:40:35
tags: - Mysql
description:
---

- 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数

````sql
SELECT
	* 
FROM
	( SELECT * FROM sc WHERE sc.CId = '01' ) AS t1,
	( SELECT * FROM sc WHERE sc.CId = '02' ) AS t2 
WHERE
	t1.SId = t2.SId 
	AND t1.score > t2.score
SELECT
	* 
FROM
	( SELECT * FROM sc WHERE sc.CId = '01' ) AS t1,
	( SELECT * FROM sc WHERE sc.CId = '02' ) AS t2 
WHERE
	t1.SId = t2.SId 
	AND t1.score > t2.score

````

- 查询同时存在" 01 "课程和" 02 "课程的情况

````sql
select * from 
    (select * from sc where sc.CId = '01') as t1, 
    (select * from sc where sc.CId = '02') as t2
where t1.SId = t2.SId;
````

- 查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null )

````sql
SELECT
	* 
FROM
	( SELECT * FROM sc WHERE sc.CId = '01' ) AS t1
	LEFT JOIN ( SELECT * FROM sc WHERE sc.CId = '02' ) AS t2 ON t1.SId = t2.SId;
````

- 查询不存在" 01 "课程但存在" 02 "课程的情况

````sql
SELECT
	* 
FROM
	sc sc1 
WHERE
	sc1.CId = '02' 
	AND sc1.SId NOT IN ( SELECT SId FROM sc WHERE sc.CId = '01' )
````

- 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

````sql
SELECT * FROM student tb1,(SELECT SId,AVG(score) ag FROM sc GROUP BY SId HAVING AVG(score) > 60) tb2 WHERE tb1.sid = tb2.sid 
````

- 查询在 SC 表存在成绩的学生信息

````sql
SELECT DISTINCT
	student.* 
FROM
	student,
	sc 
WHERE
	student.SId = sc.SId
````

- 查询所有同学的学生编号、学生姓名、选课总数、所有课程的成绩总和

````sql
SELECT
	student.sid,
	student.sname,
	r.coursenumber,
	r.scoresum 
FROM
	student,(
	SELECT
		sc.sid,
		sum( sc.score ) AS scoresum,
		count( sc.cid ) AS coursenumber 
	FROM
		sc 
	GROUP BY
		sc.sid 
	) r 
WHERE
	student.sid = r.sid;
````

-  查有成绩的学生信息

**方式一**：适用于B表比A表数据小的情况

````sql
SELECT
	* 
FROM
	student 
WHERE
	SId IN (
	SELECT
		SId 
FROM
	sc)
````

**方式二**：适用于B表比A表数据大的情况

````sql
SELECT
	* 
FROM
	student 
WHERE
	EXISTS (
	SELECT
		SId 
	FROM
		sc 
WHERE
	student.SId = sc.SId)
````

- 查询「李」姓老师的数量

````sql
SELECT
	COUNT( 1 ) 
FROM
	teacher 
WHERE
	Tname LIKE '李%'
````

- 查询学过「张三」老师授课的同学的信息

````sql
SELECT
	* 
FROM
	student,
	sc,
	course,
	teacher 
WHERE
	student.SId = sc.SId 
	AND course.CId = sc.CId 
	AND course.TId = teacher.TId 
	AND Tname = '张三'
````

- 查询没有学全所有课程的同学的信息

````sql
SELECT
	* 
FROM
	student 
WHERE
	sid NOT IN (
	SELECT
		sid 
	FROM
		sc 
	GROUP BY
		SId 
HAVING
	COUNT( CId ) = ( SELECT count( CId ) FROM course ))
````

- 查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息

````sql
SELECT
	* 
FROM
	student 
WHERE
	SId IN (
	SELECT DISTINCT
		SId 
	FROM
		sc 
WHERE
	CId IN ( SELECT CId FROM sc WHERE SId = '01' ))
````

- 查询和" 01 "号的同学学习的课程完全相同的其他同学的信息

````sql
SELECT
	* 
FROM
	student 
WHERE
	sid IN (
	SELECT
		SId 
	FROM
		sc 
	WHERE
		CId IN ( SELECT CId FROM sc WHERE SId = '01' ) 
	GROUP BY
		SId 
	HAVING
	COUNT( SId ) = ( SELECT COUNT( CId ) FROM sc WHERE SId = '01' )) 
	AND sid != '01'
````

- 查询没学过"张三"老师讲授的任一门课程的学生姓名

**多表嵌套查询**

````sql
SELECT
	* 
FROM
	student 
WHERE
	sid NOT IN (
	SELECT DISTINCT
		sid 
	FROM
		sc 
	WHERE
		cid IN (
		SELECT
			cid 
		FROM
			course 
		WHERE
		tid = ( SELECT tid FROM teacher WHERE tname = '张三' )))
````

**联合查询**

````sql
SELECT
	* 
FROM
	student 
WHERE
	sid NOT IN (
	SELECT
		sid 
	FROM
		teacher,
		course,
		sc 
	WHERE
		teacher.TId = course.TId 
		AND sc.CId = course.cid 
	AND teacher.Tname = '张三' 
	)
````

- 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

````sql
SELECT
	tb1.SId,
	tb1.sname,
	tb2.avg 
FROM
	student tb1
	RIGHT JOIN (
	SELECT
		sid,
		avg( score ) avg 
	FROM
		sc 
	WHERE
		sid IN ( SELECT sid FROM sc WHERE score < 60 GROUP BY sid HAVING count( 0 ) > 1 ) 
	GROUP BY
	sid 
	) tb2 ON tb1.sid = tb2.sid
````

- 检索" 01 "课程分数小于 60，按分数降序排列的学生信息

````sql
SELECT
	tb1.*,
	tb2.sid,
	tb2.score 
FROM
	student tb1
	RIGHT JOIN ( SELECT sid, score FROM sc WHERE cid = '01' AND score < 60 ) tb2 ON tb1.sid = tb2.sid 
ORDER BY
	tb2.score DESC
````

````sql
SELECT
	tb1.*,
	tb2.sid,
	tb2.score 
FROM
	student tb1,
	sc tb2 
WHERE
	tb2.cid = '01' 
	AND tb2.score < 60 
	AND tb1.sid = tb2.sid 
ORDER BY
	tb2.score DESC
````



- 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

````sql
SELECT
	* 
FROM
	sc tb1
	LEFT JOIN ( SELECT sid, avg( score ) avg FROM sc GROUP BY sid ) tb2 ON tb1.sid = tb2.sid 
ORDER BY
	avg DESC;
````

- 查询各科成绩最高分、最低分和平均分

````sql
SELECT
	cid,
	max( score ),
	min( score ),
	avg( score ) 
FROM
	sc 
GROUP BY
	cid
	 
````

- 按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺

````sql

````

- 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺(问题)

````sql
set @crank=0;
select q.sid, total, @crank := @crank +1 as rank from(
select sc.sid, sum(sc.score) as total from sc
group by sc.sid
order by total desc)q;
````

- 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比

````sql
SELECT c.cid,c.cname 
,((SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid AND sc.score<=100 AND sc.score>80)/(SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid )) "100-85"
,((SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid AND sc.score<=85 AND sc.score>70)/(SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid )) "85-70"
,((SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid AND sc.score<=70 AND sc.score>60)/(SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid )) "70-60"
,((SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid AND sc.score<=60 AND sc.score>=0)/(SELECT COUNT(1) FROM  sc WHERE sc.cid=c.cid )) "60-0"
FROM course c ORDER BY c.cid

````

- 查询各科成绩前三名的记录

````sql

````

- 查询每门课程被选修的学生数

````sql
SELECT cid,count(sid) count FROM sc GROUP BY cid 
````

- 查询出只选修两门课程的学生学号和姓名

````sql
SELECT tb1.sid, tb1.sname FROM student tb1,( SELECT SId FROM sc GROUP BY SId HAVING COUNT( cid ) = 2 ) tb2 
WHERE
	tb1.SId = tb2.SId
````

- 查询男生、女生人数

````sql
SELECT
	Ssex,
	count( sid ) 
FROM
	student 
GROUP BY
	Ssex
````

- 查询名字中含有「风」字的学生信息

````sql
SELECT * FROM student WHERE Sname LIKE '%风%'
````

- 查询同名同性学生名单，并统计同名人数

````sql
SELECT
	sname,
	Ssex 
FROM
	student 
GROUP BY
	Sname,
	Ssex 
HAVING
	count( sid ) > 1
````

- 查询 1990 年出生的学生名单

````sql
SELECT * FROM student WHERE Year(sage) = 1990
````

- 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

````sql
SELECT
	cid,
	avg( score ) avg_score 
FROM
	sc 
GROUP BY
	cid 
ORDER BY
	avg_score ASC
````

- 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩

````sql
SELECT
	sid,
	avg( score ) avg_score 
FROM
	sc 
GROUP BY
	sid 
HAVING
	avg_score > 85

````

- 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数

````sql
SELECT
	tb1.Sname,
	tb2.score 
FROM
	student tb1,(
	SELECT
		sid,
		score 
	FROM
		sc 
	WHERE
		cid = ( SELECT cid FROM course WHERE Cname = '数学' ) 
		AND score < 60 
	) tb2 
WHERE
	tb1.sid = tb2.sid
````

- 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）

````sql
SELECT
	* 
FROM
	student
	LEFT JOIN sc ON student.sid = sc.sid
````

- 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数

````sql
SELECT
	student.Sname,
	course.Cname,
	sc.score 
FROM
	sc,
	course,
	student 
WHERE
	sc.CId = course.CId 
	AND sc.SId = student.SId 
	AND sc.score > 70
````

- 查询存在不及格的课程

````sql
SELECT DISTINCT
	( course.Cname ) 
FROM
	sc,
	course 
WHERE
	sc.CId = course.CId 
	AND sc.score < 70

````

- 查询课程编号为 01 且课程成绩在 80 分及以上的学生的学号和姓名

````sql
SELECT
	student.SId,
	student.Sname,
	sc.score 
FROM
	sc,
	student 
WHERE
	sc.CId = "01" 
	AND sc.SId = student.SId 
	AND sc.score >= 80
````

- 求每门课程的学生人数

````sql
SELECT
	sc.CId,
	count( 1 ) 
FROM
	sc 
GROUP BY
	CId
````

- 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

````sql
SELECT
	sc.SId,
	sc.score 
FROM
	sc,
	course,
	teacher 
WHERE
	sc.CId = course.CId 
	AND teacher.TId = course.TId 
	AND teacher.Tname = '张三' 
ORDER BY
	sc.score DESC 
	LIMIT 0,1
````

- 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

````sql
SELECT
	Student.*,
	SC.score 
FROM
	Student,
	SC 
WHERE
	Student.Sid = SC.Sid 
	AND score = (
	SELECT
		SC.score 
	FROM
		SC,
		Course,
		Teacher 
	WHERE
		SC.Cid = Course.Cid 
		AND Course.Tid = Teacher.Tid 
		AND Teacher.Tname = '张三' 
	ORDER BY
		SC.score DESC 
	LIMIT 1 
	);
````

- 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

````
SELECT DISTINCT
	tb1.* 
FROM
	sc tb1
	INNER JOIN sc tb2 
WHERE
	tb1.SId = tb2.SId 
	AND tb1.CId != tb2.CId 
	AND tb1.score = tb2.score
````

- 查询每门成绩最好的前两名

````

````

