后果自负，不保证正确性。欢迎发issue~
# 简单查询

1. ```sql
	select * from STUDENT;
	```

2. ```sql
	select SNO, SNAME, SBIRTHDAY from STUDENT where SSEX = '男';
	```

3. ```sql
	alter table STUDENT add ADDR varchar(20);
	alter table STUDENT modify ADDR varchar(25);
	```

4. ```sql
	alter table STUDENT REGISTER_DATE default sysdate;
	alter table STUDENT drop column REGISTER_DATE;
	```

5. ```sql
	alter table STUDENT add AGE integer default 18;
	```

6. ```sql
	alter table STUDENT add unique (SNAME);
	```

7. ```sql
	create index COURSE_INDEX  on COURSE (CNO, CNO desc);
	```

8. ```sql
	alter table STUDENT add constraint check (
	    case when ssex = '男' and age < 23 then 1  
    	when ssex = '女' and age < 21 then 1 
	    else 0 end = 1  )
	```
	
9. ```sql
	create view VIEW_80 asselect sno, cno, grade from sc where grade > 80 with check option
	```

10. ```sql
	select * from VIEW_80 where GRADE > 90;
	```

11. ```sql
	insert into VIEW_80 values(...);
	```

12. ```sql
	insert into VIEW_80 values(...);
	```

13. ```sql
	update VIEW_80  set  GRADE = 90 where SNO =  ? and CNO = ?;
	```

14. ```sql
	update VIEW_80  set  GRADE = 90 where SNO =  ? and CNO = ?;
	```

15. ```sql
	select SNO, SNAME, SSEX, SBIRTHDAY from STUDENT where SSEX = '女' and SBIRTHDAY < to_date('1980-01-01', 'yyyy-mm-dd');
	```

16. ```sql
	select TEACHING.TNO, TNAME, count(TEACHING.TNO) as english_classes from TEACHER, TEACHING
	where TEACHING.TNO = TEACHER.TNO and LANGUAGE = 'English'
	group by TEACHING.TNO, TNAME
	```
	
17. ```sql
	select TNO, TNAME, TTITLE from TEACHER where TTITLE != '讲师';
	```

18. ```sql
	select distinct SNO from SC where GRADE is null;
	```

19. ```sql
	select SNO, GRADE from SC where GRADE < 60 order by GRADE desc;
	```

20. ```sql
	select TNO, TNAME, TBIRTHDAY from TEACHER where to_char(TBIRTHDAY, 'yyyy') = '1970';
	```

21. ```sql
	select CNO, count(SNO) from SC group by CNO;
	```

22. ```sql
	select TNO from TEACHING group by TNO having count(TNO) > 2;
	```

23. ```sql
	select avg(GRADE) as avg, min(GRADE) as min, max(GRADE) as max from SC where CNO = '800001';
	```

24. ```sql
	select TNAME, TBIRTHDAY from TEACHER
	where TBIRTHDAY > to_date('1960', 'yyyy') and TTITLE = '讲师' order by TBIRTHDAY;
	```

# 复杂查询

1.  ```sql
	create view NEW_VIEW as
	select STUDENT.SNO, SNAME, CLASSNO, CNAME, GRADE from STUDENT, SC, COURSE
	where STUDENT.SNO = SC.SNO and SC.CNO = COURSE.CNO
	```

2.  ```sql
	select SNO, SNAME, CNAME, GRADE from NEW_VIEW where CLASSNO = 'Rj0801';
	```

3.  ```sql
	insert into new_view(sno,sname,classno,cname)
	values('08300010','李在','Rj0801','数据库系统')
	```

4.  ```sql
	select STUDENT.SNO, SNAME, sum(CCREDIT)
	from STUDENT join SC on STUDENT.SNO = SC.SNO
	join COURSE on SC.CNO = COURSE.CNO
	where GRADE >= 60 group by STUDENT.SNO, SNAME;
	```

5.  ```sql
	select STUDENT.SNO, SNAME, avg(GRADE) as avg_grade, count(CNO) as total_classes from STUDENT 
	join SC on STUDENT.SNO = SC.SNO 
	group by STUDENT.SNO, SNAME;
	```

6.  ```sql
	select STUDENT.SNO, SNAME, SC.CNO, CNAME from STUDENT 
	join SC on STUDENT.SNO = SC.SNO 
	join COURSE on SC.CNO = COURSE.CNO 
	where GRADE is null;
	```

7.  ```sql
	select SC.SNO, SNAME, SC.CNO, CNAME, GRADE
	from SC join STUDENT on SC.SNO = STUDENT.SNO
	join COURSE on SC.CNO = COURSE.CNO
	where GRADE < 60;
	```

8.  ```sql
	select SNAME, GRADE from NEW_VIEW where CNAME = '程序设计语言';
	or select SNAME, GRADE from STUDENT, SC, COURSE where STUDENT.SNO = SC.SNO and SC.CNO = COURSE.CNO and CNAME = '程序设计语言';
	```

9.  ```sql
	select SC.SNO, SNAME, CLASSNAME, SC.CNO, CNAME, GRADE 
	from STUDENT, CLASS, SC, COURSE 
	where STUDENT.SNO = SC.SNO and SC.CNO = COURSE.CNO and STUDENT.CLASSNO = CLASS.CLASSNO 
	and CLASSDEPT = '软件开发';
	```

10. ```sql
	select TNAME, CNAME from TEACHER join TEACHING on TEACHER.TNO = TEACHING.TNO join COURSE on TEACHING.CNO = COURSE.CNO;
	```

11. ```sql
	select st.SNO, st.SNAME, co.CNAME, temp.avg - sc.grade
	from STUDENT st, COURSE co, SC sc, (select CNO, avg(GRADE) avg from sc group by sc.CNO ) temp 
	where st.SNO = sc.SNO and sc.CNO = co.CNO and sc.CNO = temp.CNO and temp.avg - sc.GRADE > 0;
	```

12. ```sql
	select SNAME from STUDENT
	where (select CLASSNO from STUDENT where SNAME = '葛畅') = CLASSNO and SNAME != '葛畅';
	```

13. ```sql
	select distinct SNAME from STUDENT st, SC sc, COURSE co
	where st.SNO = sc.SNO and sc.CNO = co.CNO
	and not exists(
		select * from sc 
		where SNO = st.SNO
	and sc.CNO = (select CNO from COURSE where CNAME = '计算机基础')
	);
	```

14. ```sql
	select TNAME from TEACHER, TEACHING, COURSE
	where TEACHER.TNO = TEACHING.TNO and TEACHING.CNO = COURSE.CNO
	and COURSE.CNAME = '数据库系统'
	union
	select TNAME from TEACHER, TEACHING, COURSE
	where TEACHER.TNO = TEACHING.TNO and TEACHING.CNO = COURSE.CNO
	and COURSE.CNAME = '数据结构'
	```

15. ```sql
	select distinct TNAME from TEACHER, TEACHING where TEACHING.TNO = TEACHER.TNO;
	```

16. ```sql
	select SNAME from STUDENT st, SC where st.SNO = SC.SNO and SSEX = '女' 
	and exists(select * from SC where SNO = st.SNO and CNO = '800001') 
	and exists(select * from SC where SNO = st.SNO and CNO = '800002')
	```

17. ```sql
	select st.sname from student st,SC
	where st.sno=SC.sno
	and not exists(select * from SC where st.SNO = SC.SNO and CNO = '800001')
	and not exists(select * from SC where st.SNO = SC.SNO and CNO = '800002')
	```

18. ```sql
	select distinct SNAME from STUDENT, SC where SSEX = '女' and STUDENT.SNO = SC.SNO and GRADE = 95;
	```

19. ```sql
	select SNAME from STUDENT, SC where STUDENT.SNO = SC.SNO group by SNAME having count(SC.SNO) > 3;
	```

20. ```sql
	select distinct SNAME from STUDENT, SC 
	where STUDENT.SNO = SC.SNO and SSEX = '男' 
	group by SC.SNO, SNAME having avg(GRADE) > 80;
	```
	
21. ```sql
	select SC.CNO, avg(GRADE) from SC, TEACHER, TEACHING 
	where SC.CNO = TEACHING.CNO and TEACHING.TNO = TEACHER.TNO and TNAME = '徐永军' group by SC.CNO;
	```

22. ?

23. ```sql
	select SNAME from STUDENT st, SC where st.SNO = SC.SNO and 90 < all (select GRADE from SC where SC.SNO = st.SNO);
	```

24. ```sql
	select SNAME from STUDENT where SSEX = '男' and SBIRTHDAY < all (select SBIRTHDAY from SC where SSEX = '女');
	```

25. ```sql
	select SNAME from STUDENT st, SC where st.SNO = SC.SNo and SSEX = '女' and not exists(select * from SC where st.SNO = SC.SNO and CNO = '800002');
	```

26. ```sql
	select distinct SNAME from STUDENT st, SC where st.SNO = SC.SNO and 60 < all (select GRADE from SC where st.SNO = SC.SNO);
	```

27. ```sql
	select SNAME from STUDENT st, SC where st.SNO = SC.SNO and SC.CNO = all(select CNO from COURSE);
	```

28. ```sql
	select SNAME from STUDENT st
	where not exists(
	    select CNO from SC, STUDENT where SNAME = '葛畅' and SC.SNO = STUDENT.SNO
	    and CNO not in (select CNO from SC where SC.SNO = st.SNO)
	) and SNAME != '葛畅';
	```

29. ```sql
	select SNAME, avg(GRADE) from SC, STUDENT
	having avg(GRADE)=(select max(avg(GRADE)) from SC group by SC.SNO)
	group by SNAME, SC.SNO;
	```

30. ```sql
	select * from STUDENT st
	where st.SNO in (
		select SNO from SC 
		group by SNO
		having avg(GRADE) > (
			select avg(GRADE) from SC, STUDENT
			where SC.SNO = STUDENT.SNO
				and st.CLASSNO = STUDENT.CLASSNO
		)
	)
	//直接select两次比较也行
	//也许存在效率问题
	select * from STUDENT st
	where
	(select avg(GRADE) from SC where SC.SNO = st.SNO) 
	> 
	(select avg(GRADE) from SC, STUDENT where SC.SNO = STUDENT.SNO and st.CLASSNO = STUDENT.CLASSNO)
	```

略代表暂时想不清

# 更新操作

1. ```sql
	update sc set grade=grade*1.05
	where sno in (
		select sno
		from sc,teaching,teacher,student
		where sc.cno=teaching.cno and teaching.tno=teacher.tno and teacher.tname='徐永军'
		and ssex = '女'
	)
	```


2. ```sql
	insert into stud
	select student.sno,sname,ssex from student,sc 
	where student.sno=sc.sno and sc.grade>80	
	```

3. ```sql
	delete from sc where grade is null;
	```

4. ```sql
	delete from sc where sno = (select sno from student where sname = '王威')
	```

5. ```sql
	update sc set grade = null 
	where grade < 60 and cno = (select cno from course where cname = '数据结构')
	```

6. ```sql
	update sc set grade=grade*1.05
	where sno in (select sno from student where ssex = '女') and grade < (select avg(grade) from sc)	
	```

7. ```sql
	update sc set grade=grade*1.05
	where cno = '800004' and grade < 75;
	update sc set grade=grade*1.04
	where cno = '800004' and grade >= 75;
	```

# 定义存储过程

1. ```plsql
	create or replace procedure insert_student (
	    no varchar2,
	    name varchar2,
	    sex varchar2,
	    birthday date,
	    class_no varchar2
	)
	is
	begin
	    insert into STUDENT(SNO, SNAME, SSEX, SBIRTHDAY, CLASSNO)
	        values (no, name, sex, birthday, class_no);
	end;
	
	
	/* execute insert_student('17301117', 'yuri', '男', sysdate, 'Rj0801'); */
	```

2. ```plsql
	create or replace procedure insert_course (
	    no varchar2,
	    name varchar2,
	    credit number := 4
	)
	is
	begin
	    insert into COURSE(cno, cname, ccredit) values (no, name, credit);
	end;
	
	/* execute insert_course('700001', '??'); */
	/* execute insert_course('700002', '???', 3); */
	```

3. ```plsql
	create or replace procedure query_student (
	    no varchar2,
	    name out varchar2,
	    sex out varchar2,
	    birthday out date,
	    class_no out varchar2
	)
	is
	begin
	    select SNAME, SSEX, SBIRTHDAY, CLASSNO
	    into name, sex, birthday, class_no
	    from STUDENT where SNO = no ;
	end;
	
	/**
	variable name varchar2(8);
	variable sex varchar2(3);
	variable birthday varchar2(20);
	variable classno varchar2(6);
	execute query_student('17301117', :name, :sex, :birthday, :classno);
	execute dbms_output.put_line(:name);
	*/
	```

4. ```plsql
	create or replace procedure output_avg_grade_over_80
	is
	    cursor student_cursor is
	        select SNAME, SSEX, AGE, avg(GRADE) as avg from STUDENT, SC
	            where SC.SNO = STUDENT.SNO
	            group by SC.SNO, SNAME, SSEX, AGE
	            having avg(GRADE)  > 80;
	begin
	    for student_record in student_cursor
	    loop
	        exit when student_cursor% notfound;
	        dbms_output.put_line(
	            ' name: '||student_record.SNAME||
	            ' sex:'||student_record.SSEX||
	            ' age'||student_record.AGE||
	            ' avg_grade: '||student_record.avg);
	    end loop;
	end;
	
	/**
	execute output_avg_grade_over_80;
	*/
	```

5. ```plsql
	create or replace procedure select_all_sb_all_course (
	    name varchar2
	)
	is
	    cursor students is
	        select st.SNO,
	               SNAME,
	               (select count(CNO) from SC where SC.SNO = st.SNO) as count,
	               (select avg(GRADE) from SC where SC.SNO = st.SNO) as avg_grade,
	               TOTALCREDIT
	        from STUDENT st
	        where not exists(
	                select * from SC, STUDENT
	                where SNAME = name and SC.SNO = STUDENT.SNO
	                    and CNO not in (
	                        select CNO from SC where SC.SNO = st.SNO
	                    )
	        ) and SNAME != name;
	
	begin
	    for student in students
	        loop
	            dbms_output.put_line(
	                        ' no: '||student.SNO||
	                        ' name: '||student.SNAME||
	                        ' course count: '||student.count||
	                        ' avg grade: '||student.avg_grade||
	                        ' total credit: '||student.TOTALCREDIT);
	        end loop;
	end;
	
	/**
	execute select_all_sb_all_course('葛畅');
	*/
	```

6. ```plsql
	create or replace procedure query_lower (
	    class varchar2
	)
	is
	    cursor students is
	        select st.SNO, SNAME, avg(GRADE) as avg
	        from SC, STUDENT st
	        where CLASSNO = class
	        group by st.SNO, SNAME
	        having avg(GRADE) < (select avg(GRADE) from SC, STUDENT where STUDENT.CLASSNO = class and SC.SNO = STUDENT.SNO);
	begin
	    for student in students
	    loop
	        dbms_output.put_line(
	            'no: '||student.SNO||
	            ' name: '||student.SNAME||
	            ' avg grade: '||student.avg
	        );
	    end loop;
	end;
	
	/**
	execute query_lower('Rj0801');
	*/
	```

# 触发器

1. ```plsql
	create or replace trigger display_trigger
	    after insert on STUDENT
	    declare
	        rc sys_refcursor;
	    begin
	        open rc for select * from STUDENT;
	        dbms_sql.return_result(rc);
	    end display_trigger;
	```

2. ```plsql
	create or replace trigger add_student_into_class
	    after insert on STUDENT
	    for each row
	    declare
	    begin
	        update CLASS set STUDENTNUMBER = STUDENTNUMBER + 1
	        where CLASSNO = :new.CLASSNO;
	    end;
	    
	/**
	execute insert_student('17305555', 'wtf', '男', sysdate, 'Rj0801');
	*/
	```

3. ```plsql
	create or replace trigger delete_student_from_class
	    after delete on STUDENT
	    for each row
	    declare
	    begin
	        update CLASS set STUDENTNUMBER = STUDENTNUMBER - 1 where CLASSNO = :old.CLASSNO;
	    end;
	    
	/**
	delete from student where sname = 'wtf';
	*/
	```

4. ```plsql
	create or replace trigger move_student_between_class
	    after update of CLASSNO on STUDENT
	    for each row
	    declare
	    begin
	        update CLASS set STUDENTNUMBER = STUDENTNUMBER + 1 where CLASSNO = :new.CLASSNO;
	        update CLASS set STUDENTNUMBER = STUDENTNUMBER - 1 where CLASSNO = :old.CLASSNO;
	    end;
	```

5. ```plsql
	create or replace trigger insert_invalid_student_into_sc
	    before insert on SC
	    for each row
	    declare
	        v_cnt number;
	    begin
	        select count(*)
	            into v_cnt
	            from dual
	            where exists(select * from STUDENT where SNO = :new.SNO);
	        if v_cnt = 0 then
	            insert into STUDENT(SNO, SNAME) values (:new.SNO, 'NULL');
	        end if;
	    end;
	    
	/**
	insert into sc values('17306666', '800003', 100);
	*/
	```

6. ```plsql
	create or replace trigger decrease_student_grade
	    before update of GRADE on SC
	    for each row
	    declare
	    begin
	        if :old.GRADE > :new.GRADE then
	            raise_application_error(-20001, 'You can''t decrease grade');
	        end if;
	    end;
	    
	/**
	 update sc set grade = 90 where sno = '17306666';
	*/
	```

7. ```plsql
	create or replace trigger insert_over_3_class
	    before insert on SC
	    for each row
	    declare
	        v_classes number;
	    begin
	        select count(*) into v_classes from SC where SNO = :new.SNO;
	        if v_classes > 3 then
	            raise_application_error(-20002, 'Over 3 classes');
	        end if;
	    end;
	```

8. ```plsql
	create or replace trigger insert_delete_sc_course
	    after insert or delete
	    on SC
	    for each row
	declare
	    v_credit number;
	begin
	    if deleting then
	        select CCREDIT into v_credit from COURSE where CNO = :old.CNO;
	        update STUDENT set TOTALCREDIT = TOTALCREDIT + v_credit where SNO = :old.SNO;
	    elsif inserting then
	        select CCREDIT into v_credit from COURSE where CNO = :new.CNO;
	        update STUDENT set TOTALCREDIT = TOTALCREDIT - v_credit where SNO = :new.SNO;
	    end if;
	end;
	
	/**
	insert into sc values('17306666', '800001', 100);
	*/
	```
