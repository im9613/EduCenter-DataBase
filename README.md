# EduCenter-DataBase(교육센터 데이터베이스 설계)

## 프로젝트 개요
교육센터 운영에 필요한 데이터베이스를 설계 후 구현한다. 기능 사용자는 학생, 교사, 관리자 3개의 역할로 나누어진다. 3개의 역할이 공통으로 접근하는 데이터와, 역할별로 나누어지는 데이터를 구분하여 데이터베이스 설계.

<br>

## 주요기능
- **과정 구성**: 교육과정 생성 시, 과정에 포함되는 과목들을 달리하여 교육과정 생성
- **교육생 관리**: 스케줄 조회 기능을 통해 강의 스케줄 조회, 출결 관리
- **강사 관리**: 스케줄 조희

 ### 진행 기간
- 2024.08.06 ~ 2024.08.21 (2주)
  
<br>

## 개발환경 및 사용언어
- **언어**: SQL
- **개발 환경**: Windows 11, JDK11, SQL Developer, Oracle Database 11c
- **기타**: ERDCloud, draw.io

<br>

## 담당 업무
- 관리자기능
  - 물품대여, 분실물 센터 관리 데이터베이스 설계
  - 교사 및 과정 평가 점수 관리 데이터베이스 설계
  - 취득 자격증 목록 관리 데이터베이스 설계
  - 공지 게시판 데이터베이스 설계
  - 스터디 그룹 생성 및 스터디룸 대여 관리 데이터베이스 설계
  

  <br>

## ERDCloud 테이블 설계
  ![ERD](https://github.com/im9613/EduCenter-DataBase/blob/main/EduPic/centerERD.png)

  <br>

## 사용한 쿼리문(ANSI SQL)
```
교사 평가 결과 관리 (등록, 수정, 삭제, 조회)

– 등록
INSERT INTO tblTeacherRating   
      VALUES (teahcer_seq.NEXTVAL,10, 1, 3, 5, 3, 1, 5, '학습자료가 더 필요해요');
– 수정
UPDATE tblTeacherRating 
      SET q1 = 3 
            WHERE seq = 1;
– 삭제
DELETE
      FROM tblTeacherRating 
        WHERE teacher_seq = 1;
– 조회 (교사번호, 이름, 교사 만족도, 교사 건의 사항)
SELECT
    t.seq AS 교사번호,
    t.name as 교사명,
    tr.q1,
    tr.q2,
    tr.q3,
    tr.q4,
    tr.q5,
    tr.suggestion
FROM tblTeacherRating tr
    INNER JOIN tblTeacher t
        ON tr.teacher_seq = t.seq
    ORDER BY 교사번호;
```

```
수강 중인 교육생 또는 수료 후 교육생이 과정 평가를 진행하고 관리자는 평가 결과를 관리 한다.
  (등록, 수정, 삭제, 조회)

– 등록
INSERT INTO tblClassRating 
      VALUES (seqClassRating.NEXTVAL, 36, 1008, 1, 1, 1, 1, 1, '구성이 허술한.');
– 수정
UPDATE tblClassRating 
      SET q1 = 5
            WHERE seq = 1009;
– 삭제
DELETE
      FROM tblClassRating 
            WHERE seqClassRating = 1009;
– 조회 (과정명, 과정 만족도, 과정 건의사항)
SELECT
      c.name,
      cr.q1,
      cr.q2,
      cr.q3,
      cr.q4,
      cr.q5,
      cr.suggestion
FROM tblOpenClass oc
      JOIN tblClass c 
            ON oc.class_seq = c.seq
                  LEFT JOIN tblClassRating cr 
                       ON oc.seq = cr.openclass_seq;
```
```
. 스터디 룸 사용 내역을 등록, 수정, 삭제, 조회 할 수 있다.

– 등록
INSERT INTO tblBookingInfo VALUES (
    BOOKINGINFO_SEQ.NEXTVAL,
    2,
    TO_TIMESTAMP('2024-02-12 14:57:38', 'YYYY-MM-DD HH24:MI:SS'),
    TO_DATE('2024-02-23', 'YYYY-MM-DD'),
    TO_TIMESTAMP('2024-02-23 18:15:38', 'YYYY-MM-DD HH24:MI:SS'),
    TO_TIMESTAMP('2022-01-12 19:27:14', 'YYYY-MM-DD HH24:MI:SS')
);
– 수정
UPDATE tblBookingInfo
    SET classroom_seq = 6
        WHERE seq = 141;
– 삭제 
DELETE
    FROM tblBookingInfo
        WHERE seq = 141;
– 조회
1.	스터디 룸의 정보는 강의실과 동일하며 이는 아래와 같다.
a.	스터디 룸 이름
b.	스터디 룸 최대 수용 인원
2.	관리자는 스터디 룸 예약 정보를 관리한다.
출력되는 예약 정보는 아래와 같다.
a.	강의실 번호 (스터디룸 번호)
b.	대여 신청일
c.	대여 신청 시각
d.	스터디룸 이용 날짜
e.	이용 시작 시각
f.	이용 종료 시각
g.	사용 인원
SELECT
    c.name as 강의실이름,
    c.capacity as 수용인원,
    bi.confirm_date as 신청날짜,
    to_char(bi.confirm_date, 'hh24:mi:ss') as 신청시각,
    bi.reserve_date as 예약날짜,
    to_char(bi.start_time, 'hh24:mi:ss')  as 이용시각,
    to_char(bi.end_time, 'hh24:mi:ss') as 종료시각,
    count(sbi.student_seq) as 이용학생수
FROM tblStudent_BookingInfo sbi
    JOIN tblBookingInfo bi
        ON sbi.bookinginfo_seq = bi.seq
            LEFT JOIN tblStudent s
                ON sbi.student_seq = s.seq
                    LEFT JOIN tblClassroom c
                        ON bi.classroom_seq = c.seq
GROUP BY
  
    c.name,
    c.capacity,
    bi.confirm_date,
    to_char(bi.confirm_date, 'hh24:mi:ss') ,
    bi.reserve_date,
    to_char(bi.start_time, 'hh24:mi:ss'),
    to_char(bi.end_time, 'hh24:mi:ss')
ORDER BY 강의실이름, 예약날짜, 이용시각  ASC;

1.	스터디 룸 사용 내역에 교육생의 이름, 과정명을 등록, 수정, 삭제할 수 있다.
SELECT 
    DISTINCT(SI.name),
    CS.name AS 이름,
    CR.name AS 과정명,
    BI.reserve_date AS 예약날짜,
    BI.start_time AS 이용시각,
    BI.end_time AS 종료시각
    
FROM tblStudent_BookingInfo SBI
    INNER JOIN tblBookingInfo BI
        ON SBI.bookinginfo_seq = BI.seq
            INNER JOIN tblClassroom CR
                ON BI.classroom_seq = CR.seq
                    INNER JOIN tblOpenClass OC
                        ON CR.seq = OC.classroom_seq
                            INNER JOIN tblStudent S
                                ON OC.seq = S.openclass_seq
                                    INNER JOIN tblStudentInfo SI
                                        ON S.studentinfo_seq = SI.seq                                        
                                            INNER JOIN tblClass CS
                                                ON OC.class_seq = CS.seq
```

## 오브젝트  스크립트(PL/SQL)
```
--교사평가 등록
CREATE OR REPLACE PROCEDURE teahcer_rating_insert (
    pseq IN NUMBER,
    pteacher_seq IN NUMBER,
    pstudent_seq IN NUMBER,
    pq1 IN NUMBER,
    pq2 IN NUMBER,
    pq3 IN NUMBER,
    pq4 IN NUMBER,
    pq5 IN NUMBER,
    psuggestion IN VARCHAR2
)
IS
BEGIN
    INSERT INTO tblTeacherRating (
        seq,
        teacher_seq,
        student_seq,
        q1,
        q2,
        q3,
        q4,
        q5,
        suggestion
    )
    VALUES (
        pseq,
        pteacher_seq,
        pstudent_seq,
        pq1,
        pq2,
        pq3,
        pq4,
        pq5,
        psuggestion
    );
    
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('평가 등록 완료.');
END teahcer_rating_insert;
/
--교사평가 등록 호출
BEGIN
 teahcer_rating_insert (teacherrating_seq.NEXTVAL, 1, 1, 5, 5, 5, 5, 5, '이해가 잘가요');
END;
/
--교사평가 수정
CREATE OR REPLACE PROCEDURE teacher_rating_mod (
    pseq IN tblTeacherRating.seq%TYPE,
    pteacher_seq IN tblTeacherRating.teacher_seq%TYPE,
    psuggestion IN tblTeacherRating.suggestion%TYPE
)
IS
BEGIN
    UPDATE tblTeacherRating
        SET teacher_seq = pteacher_seq,
            suggestion = psuggestion
        WHERE seq = pseq;
    DBMS_OUTPUT.PUT_LINE('수정완료.');
    COMMIT;
    
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('수정실패.');
        ROLLBACK;
END teacher_rating_mod;
/
--교사평가 수정 호출
BEGIN
    teacher_rating_mod (
        pseq => 3001,
        pteacher_seq => 4,
        psuggestion => '친절하게 잘 설명해주십니다.'
    );
END;
/
--교사평가 삭제
CREATE OR REPLACE PROCEDURE teacher_rating_delete (
    pseq IN tblTeacherRating.seq%TYPE
)
IS
BEGIN
    DELETE FROM tblTeacherRating
        WHERE seq = pseq;
        
    DBMS_OUTPUT.PUT_LINE('삭제완료.');
    COMMIT;
    
EXCEPTION 
    WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('삭제실패.');
    ROLLBACK;
END teacher_rating_delete;
/
--교사평가 삭제 호출
BEGIN
    teacher_rating_delete (
        pseq => 3001
    );
END;
/
--교사평가 조회
CREATE OR REPLACE PROCEDURE teahcer_rating_view AS
BEGIN
    FOR c IN (
        SELECT
            t.seq AS "교사번호",
            t.name AS "교사명",
            tr.q1 AS "Q1",
            tr.q2 AS "Q2",
            tr.q3 AS "Q3",
            tr.q4 AS "Q4",
            tr.q5 AS "Q5",
            tr.suggestion AS "의견"
        FROM tblTeacherRating tr
            INNER JOIN tblTeacher t
                ON tr.teacher_seq = t.seq
    )
    LOOP
    DBMS_OUTPUT.PUT_LINE('교사번호 : '||c.교사번호);
    DBMS_OUTPUT.PUT_LINE('교사명 : '||c.교사명);
    DBMS_OUTPUT.PUT_LINE('Q1: '||c.Q1);
    DBMS_OUTPUT.PUT_LINE('Q2: '||c.Q2);
    DBMS_OUTPUT.PUT_LINE('Q3: '||c.Q3);
    DBMS_OUTPUT.PUT_LINE('Q4: '||c.Q4);
    DBMS_OUTPUT.PUT_LINE('Q5: '||c.Q5);
    DBMS_OUTPUT.PUT_LINE('의견: '||c.의견);
    DBMS_OUTPUT.PUT_LINE('----------------------------');
    END LOOP;
END teahcer_rating_view;
/
--교사평가 조회 호출
execute teahcer_rating_view;
```

```
--과정 평가 등록
CREATE OR REPLACE PROCEDURE class_rating_insert (
    pseq IN tblClassRating.seq%TYPE,
    popenclass_seq IN tblClassRating.openclass_seq%TYPE,
    pstudent_seq IN tblClassRating.student_seq%TYPE,
    pq1 IN tblClassRating.q1%TYPE,
    pq2 IN tblClassRating.q2%TYPE,
    pq3 IN tblClassRating.q3%TYPE,
    pq4 IN tblClassRating.q4%TYPE,
    pq5 IN tblClassRating.q5%TYPE,
    psuggestion IN tblClassRating.suggestion%TYPE
)
AS
BEGIN
    INSERT INTO tblClassRating (
        seq,
        openclass_seq,
        student_seq,
        q1,
        q2,
        q3,
        q4,
        q5,
        suggestion
    ) VALUES (
        pseq,
        popenclass_seq,
        pstudent_seq,
        pq1,
        pq2,
        pq3,
        pq4,
        pq5,
        psuggestion
    );
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('평가 등록 완료.');
END class_rating_insert;
/
--과정 평가 등록 호출        
BEGIN    
    class_rating_insert (seqClassRating.NEXTVAL, 1, 100, 5, 5, 5, 5, 5, '만족스럽군요..');
END;
/
--과정평가 수정
CREATE OR REPLACE PROCEDURE class_rating_mod (
    pseq IN tblClassRating.seq%TYPE,
    popenclass_seq IN tblClassRating.openclass_seq%TYPE,
    psuggestion IN tblClassRating.suggestion%TYPE
)
IS
BEGIN
    UPDATE tblClassRating
        SET openclass_seq = popenclass_seq,
            suggestion = psuggestion
        WHERE seq = pseq;
    DBMS_OUTPUT.PUT_LINE('수정완료.');
    COMMIT;
    
EXCEPTION 
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('수정실패.');
    ROLLBACK;
END class_rating_mod;
/
--과정평가 수정 호출
BEGIN
    class_rating_mod (
    pseq => 1009,
    popenclass_seq => 20,
    psuggestion => '기간이 짧아서 아쉽군요..'
    );
END;
/
--과정평가 삭제
CREATE OR REPLACE PROCEDURE class_rating_delete (
    pseq IN tblClassRating.seq%TYPE
)
IS
BEGIN
    DELETE FROM tblClassRating
        WHERE seq = pseq;
    
        DBMS_OUTPUT.PUT_LINE('삭제완료.');
    COMMIT;
    
EXCEPTION 
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('삭제실패.');
    ROLLBACK;
END class_rating_delete;
/
--과정평가 삭제 호출
BEGIN
    class_rating_delete (
        pseq => 1009
    );
END;
/
--과정 평가 조회
CREATE OR REPLACE PROCEDURE class_rating_view 
AS
BEGIN
    FOR c IN (
        SELECT
            c.name AS "과정명",
            cr.q1 AS "Q1",
            cr.q2 AS "Q2",
            cr.q3 AS "Q3",
            cr.q4 AS "Q4",
            cr.q5 AS "Q5",
            cr.suggestion AS "의견"
        FROM tblOpenClass oc
            JOIN tblClass c 
                ON oc.class_seq = c.seq
                    LEFT JOIN tblClassRating cr 
                        ON oc.seq = cr.openclass_seq
    ) LOOP
        DBMS_OUTPUT.PUT_LINE('과정명: '||c.과정명);
        DBMS_OUTPUT.PUT_LINE('Q1: '||c.Q1);
        DBMS_OUTPUT.PUT_LINE('Q2: '||c.Q2);
        DBMS_OUTPUT.PUT_LINE('Q3: '||c.Q3);
        DBMS_OUTPUT.PUT_LINE('Q4: '||c.Q4);
        DBMS_OUTPUT.PUT_LINE('Q5: '||c.Q5);
        DBMS_OUTPUT.PUT_LINE('의견: '||c.의견);
        DBMS_OUTPUT.PUT_LINE('----------------------------------------------');
    END LOOP;        
END class_rating_view;
/
--과정 평가 조회 호출
BEGIN
    class_rating_view;
END;
/
```

```
1.	교육생의 스터디 그룹을 등록, 수정, 삭제, 조회 할 수 있다.

2.	스터디 그룹의 속성은 다음과 같다
a.	구성 교육생 
b.	그룹 구성 일자
c.	그룹 구성 결심

--스터디 그룹 등록
CREATE OR REPLACE PROCEDURE studygroup_insert (
    pseq IN tblStudyGroup.seq%TYPE,
    pteacher_seq IN tblStudyGroup.teacher_seq%TYPE,
    pname IN tblStudyGroup.name%TYPE,
    pcreation IN tblStudyGroup.creation_date%TYPE,
    pgoal IN tblStudyGroup.goal%TYPE
)
IS
BEGIN
    INSERT INTO tblStudyGroup (
        seq,
        teacher_seq,
        name,
        creation_date,
        goal
    ) VALUES (
        pseq,
        pteacher_seq,
        pname,
        pcreation,
        pgoal
    );
    
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('등록 완료.');
        
END studygroup_insert;
/
--스터디 그룹 등록 호출
BEGIN
    studygroup_insert (studygroup_seq.NEXTVAL, NULL, '그룹102', '2024-08-12','재미있게 가보자');
END;
/
--스터디 그룹 수정
CREATE OR REPLACE PROCEDURE studygroup_mod (
    pseq IN tblStudyGroup.seq%TYPE,
    pteacher_seq IN tblStudyGroup.teacher_seq%TYPE,
    pname IN tblStudyGroup.name%TYPE,
    pgoal IN tblStudyGroup.goal%TYPE
)
IS
BEGIN
    UPDATE tblStudyGroup
        SET teacher_seq = pteacher_seq,
            name = pname,
            goal = pgoal
        WHERE seq = pseq;
        
            DBMS_OUTPUT.PUT_LINE('수정 완료!');
        COMMIT;

EXCEPTION 
    WHEN OTHERS THEN
            DBMS_OUTPUT.PUT_LINE('수정 실패');
        ROLLBACK;
        
END studygroup_mod;
/
--스터디 그룹 수정 호출
BEGIN
    studygroup_mod (
        pseq => 121,
        pteacher_seq => 5,
        pname => '열공단',
        pgoal => '불사르자'
    );
END;
/
--스터디 그룹 삭제
CREATE OR REPLACE PROCEDURE studygroup_delete (
    pseq IN tblStudyGroup.seq%TYPE
)
IS
BEGIN
    DELETE FROM tblStudyGroup
        WHERE seq = pseq;
    
    DBMS_OUTPUT.PUT_LINE('삭제완료');
    COMMIT;
    
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('삭제실패');
        ROLLBACK;
END studygroup_delete;
/
--스터디 그룹 삭제 호출
BEGIN
    studygroup_delete (
        pseq => 121
    );
END;
/
--스터디 그룹 등록
CREATE OR REPLACE PROCEDURE studygroup_insert (
    pseq IN tblStudyGroup.seq%TYPE,
    pteacher_seq IN tblStudyGroup.teacher_seq%TYPE,
    pname IN tblStudyGroup.name%TYPE,
    pcreation IN tblStudyGroup.creation_date%TYPE,
    pgoal IN tblStudyGroup.goal%TYPE
)
IS
BEGIN
    INSERT INTO tblStudyGroup (
        seq,
        teacher_seq,
        name,
        creation_date,
        goal
    ) VALUES (
        pseq,
        pteacher_seq,
        pname,
        pcreation,
        pgoal
    );
    
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('등록 완료.');
        
END studygroup_insert;
/
--스터디 그룹 등록 호출
BEGIN
    studygroup_insert (studygroup_seq.NEXTVAL, NULL, '그룹102', '2024-08-12','재미있게 가보자');
END;
/



– 스터디룸 등록
CREATE OR REPLACE PROCEDURE studyroom_insert (
    pseq IN tblBookingInfo.seq%TYPE,
    pclassroom IN tblBookingInfo.classroom_seq%TYPE,
    pconfirm IN tblBookingInfo.confirm_date%TYPE,
    preserve IN tblBookingInfo.reserve_date%TYPE,
    pstart IN tblBookingInfo.start_time%TYPE,
    pend IN tblBookingInfo.end_time%TYPE
)
IS
BEGIN
    INSERT INTO tblBookingInfo (
        seq,
        classroom_seq,
        confirm_date,
        reserve_date,
        start_time,
        end_time
    ) VALUES (
        pseq,
        pclassroom,
        pconfirm,
        preserve,
        pstart,
        pend
    );
    
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('등록 완료.');
        
END studyroom_insert;
/
--스터디룸 등록 호출
BEGIN
    studyroom_insert (
        BOOKINGINFO_SEQ.NEXTVAL, 
        5, 
        SYSDATE, 
        '2024-08-30', 
        TO_TIMESTAMP('2024-08-05 18:56:45', 'YYYY-MM-DD HH24:MI:SS'),
        TO_TIMESTAMP('2024-08-05 19:56:33', 'YYYY-MM-DD HH24:MI:SS'));
END;
/
--스터디룸 수정
CREATE OR REPLACE PROCEDURE studyroom_mod (
    pseq IN tblBookingInfo.seq%TYPE,
    pclassroom IN tblBookingInfo.classroom_seq%TYPE,
    pconfirm IN tblBookingInfo.confirm_date%TYPE,
    preserve IN tblBookingInfo.reserve_date%TYPE,
    pstart IN tblBookingInfo.start_time%TYPE,
    pend IN tblBookingInfo.end_time%TYPE
)
IS
BEGIN
    UPDATE tblBookingInfo
        SET 
            classroom_seq = pclassroom,
            confirm_date = pconfirm,
            reserve_date = preserve,
            start_time = pstart,
            end_time = pend
        WHERE seq = pseq;
        
             DBMS_OUTPUT.PUT_LINE('수정완료.');
        COMMIT;
   
EXCEPTION 
    WHEN OTHERS THEN
            DBMS_OUTPUT.PUT_LINE('수정실패.');
         ROLLBACK; 
END studyroom_mod;
/
--스터디룸 수정 호출
BEGIN
    studyroom_mod (
        pseq => 143,
        pclassroom => 1,
        pconfirm => sysdate,
        preserve => '2024-12-25',
        pstart => '2024-01-01',
        pend =>'2024-01-01'
    );
END;
/

--스터디룸 삭제
CREATE OR REPLACE PROCEDURE studyroom_delete (
    pseq IN tblBookingInfo.seq%TYPE
)
IS
BEGIN
    DELETE FROM tblBookingInfo
        WHERE seq = pseq;
        
        DBMS_OUTPUT.PUT_LINE('삭제완료.');
            COMMIT;
EXCEPTION 
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('삭제실패.');
            ROLLBACK;
END studyroom_delete;
/
--스터디룸 삭제 호출
BEGIN
    studyroom_delete (
        pseq => 143
    );
END;
/
--스터디룸 조회
CREATE OR REPLACE PROCEDURE studyroom_view
AS
BEGIN
    FOR c IN (
        SELECT 
            DISTINCT(SI.name) AS 학생명,
            CS.name AS 과정명,
            CR.name AS 스터디룸,
            CR.capacity AS 수용인원,
            BI.reserve_date AS 예약날짜,
            BI.start_time AS 이용시각,
            BI.end_time AS 종료시각
            
        FROM tblStudent_BookingInfo SBI
            INNER JOIN tblBookingInfo BI
                ON SBI.bookinginfo_seq = BI.seq
                    INNER JOIN tblClassroom CR
                        ON BI.classroom_seq = CR.seq
                            INNER JOIN tblOpenClass OC
                                ON CR.seq = OC.classroom_seq
                                    INNER JOIN tblStudent S
                                        ON OC.seq = S.openclass_seq
                                            INNER JOIN tblStudentInfo SI
                                                ON S.studentinfo_seq = SI.seq                                        
                                                    INNER JOIN tblClass CS
                                                        ON OC.class_seq = CS.seq
        WHERE ROWNUM <= 1000  --1000개만 출력                                                      
    )
        LOOP
        DBMS_OUTPUT.PUT_LINE('===============학생 정보 및 스터디룸 이용 내역================');
        DBMS_OUTPUT.PUT_LINE('학생명: '||c.학생명);
        DBMS_OUTPUT.PUT_LINE('과정명: '||c.과정명);
        DBMS_OUTPUT.PUT_LINE('스터디룸: '||c.스터디룸);
        DBMS_OUTPUT.PUT_LINE('수용인원: '||c.수용인원);
        DBMS_OUTPUT.PUT_LINE('예약날짜: '||c.예약날짜);
        DBMS_OUTPUT.PUT_LINE('이용시각: '||c.이용시각);
        DBMS_OUTPUT.PUT_LINE('종료시각: '||c.종료시각);
        DBMS_OUTPUT.PUT_LINE('');
       END LOOP;
  
END studyroom_view;
/
--스터디룸 조회 호출
BEGIN
    studyroom_view;
END;
/
```




## 느낀점
큰 규모의 데이터 베이스는 아니었지만, 약 50개의 데이터 테이블을 생성하고 테이블 사이의 관계를 정의하는데도 매우 복잡하다 생각했습니다. 사전에 더 치밀한 계획을 세우고 생성된 데이터간의 유효성도 고려하여 데이터 무결성을 유지해야 데이터로써의 가치가 높아질 수 있다고 생각했습니다. 또한 PL/SQL을 이용한 더미데이터, 데이터 생성을 통해 프로시저의 활용법에 대해 조금 더 알 수 있는 계기가 되었습니다. 수많은 데이터 테이블 중 원하는 데이터를 불러올 수 있게 해주는 JOIN의 사용법에 대해 더 확실하게 알게 되었습니다. PL/SQL, ANSI SQL에 대해 좀더 공부하여 데이터 조작을 용이하게 하고싶습니다.

