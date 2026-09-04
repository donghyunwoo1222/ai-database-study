# Chapter 02 확장 실습 답안 템플릿

> **과제:** 데이터와 DBMS의 기본 개념  
> **사용 방법:** 이 파일을 내려받아 본인의 GitHub 저장소에 `chapter02_answer.md`라는 이름으로 저장한 뒤 실습하면서 바로 작성합니다.  
> **제출 방법:** LMS에는 파일을 직접 업로드하지 않고, **본인 GitHub 저장소의 `chapter02_answer.md` 파일 URL**을 제출합니다.

---

## 제출 전 개인정보 주의

LMS에서 제출자를 확인할 수 있으므로 이 공개 Markdown 파일에 학번이나 실명을 반드시 적을 필요는 없습니다.

```text
GitHub 계정 또는 별칭: donghyunwoo1222
과제 작성일: 2026-09-03
사용한 AI 도구: claude
```

> 실제 비밀번호, API Key, 전체 DB 접속 URL, 개인정보가 포함된 화면은 올리지 않습니다.

---

# 1. PostgreSQL에서 현재 위치 확인

## 1-1. 실행한 SQL

```sql
SELECT version();
SELECT current_database();
SELECT current_user;
SELECT current_schema();
SHOW search_path;
```

## 1-2. 실행 결과 기록

```text
PostgreSQL 버전:PostgreSQL 18.4 on x86_64-windows, compiled by msvc-19.44.35227, 64-bit
현재 데이터베이스:postgres
현재 사용자:postgres
현재 스키마:public
search_path:"$user", public
```

## 1-3. 구조를 내 말로 설명

```text
PostgreSQL은: PostgreSQL 18.4 on x86_64-windows, compiled by msvc-19.44.35227, 64-bit 버전이다.

현재 접속한 데이터베이스는: postgres이다.

스키마는: public이다. 

DBeaver 또는 psql 같은 도구는: DBMS에 명령을 보내고 결과를 보여주는 클라이언트이다.
```

## 1-4. 계층 구조 완성

```text
사용자
→ ____________________
→ PostgreSQL DBMS
→ ____________________
→ ____________________
→ ____________________
→ 행 / 열
```

## 1-5. 증거 화면

권장 경로:

```text
assignments/chapter02/images/step01_environment.png
```

```markdown
![PostgreSQL 현재 위치 확인](./images/step01_environment.png)
```

`여기에 STEP 1 핵심 증거 화면을 삽입하세요.`

---

# 2. 데이터베이스 안의 스키마와 테이블 관찰

## 2-1. 스키마 조회 결과

실행한 SQL:

```sql
SELECT schema_name
FROM information_schema.schemata
ORDER BY schema_name;
```

관찰한 스키마 이름 중 3개 이내를 적습니다.

```text
1. pg_catalog
2. information_schema
3. public
```

### `public`은 무엇인가요?

```text
나의 설명: 별도의 스키마를 만들지 않으면 기본으로 제공되는 공용폴더
```

### 데이터베이스와 스키마는 같은 것인가요?

```text
나의 설명: 같지 않다. 스키마는 데이터베이스 안에 있으며 테이블고 같은 객체를 이름으로 구분한다.
```

## 2-2. 현재 보이는 테이블 조회

```sql
SELECT table_schema, table_name
FROM information_schema.tables
WHERE table_type = 'BASE TABLE'
  AND table_schema NOT IN ('pg_catalog', 'information_schema')
ORDER BY table_schema, table_name;
```

```text
조회된 사용자 테이블 수 또는 눈에 띈 테이블: auth_user, members, ai_evaluation

아직 테이블이 거의 없어도 괜찮은 이유: 나중에 만들거니깐 괜찮다. 
```

## 2-3. 관찰 정리

```text
PostgreSQL 서버 안에는 여러 _____데이터베이스______가 있을 수 있다.
한 데이터베이스 안에는 여러 ______스키마 ______가 있을 수 있다.
스키마 안에는 테이블과 같은 _____객체______가 존재한다.
```

---

# 3. TEMP TABLE로 테이블·행·열·키 직접 확인

## 3-1. 임시 테이블 생성 완료 확인

- [o] `ch02_students` 생성
- [o] `ch02_courses` 생성
- [o] `ch02_enrollments` 생성

각 테이블의 **한 행 의미**를 적습니다.

| 테이블 | 한 행의 의미 |
| --- | --- |
| `ch02_students` |학생 한 명 |
| `ch02_courses` |강의 한 개|
| `ch02_enrollments` |수강신청 한 개|

## 3-2. 열의 의미 확인

### `ch02_students`

| 열 | 값의 의미 | 내부 식별자 / 업무 식별자 / 일반 속성 |
| --- | --- | --- |
| `id` |학생 행을 구분하는 내부식별자|내부식별자 |
| `student_number` | 학교 업무에서 사용하는 업무 식별자 번호 | 업무식별자 |
| `name` |학생 이름 | 일반 속성 |
| `major` | 학생 이름 | 일반 속성 |

### `ch02_enrollments`

| 열 | 값의 의미 | PK / FK / 일반 속성 |
| --- | --- | --- |
| `id` | 학생 행을 구분하는 내부식별자 | PK |
| `student_id` | ch02_students 테이블에서 가져오는 FK | FK |
| `course_id` | ch02_courses 테이블에서 가져오는 FK | FK |
| `status` | 신청상태 | 속성 |

## 3-3. 입력된 행 수

```text
students 행 수: 3
courses 행 수: 2
enrollments 행 수: 3
```

## 3-4. 내부 식별자와 업무 식별자

```text
students.id가 필요한 이유: 다른 테이블에서 참조할때 정수형 PK를 FK로 사용하면, 데이터 크기가 작아, 연산 및 인덱싱 성능과 조인 속도가 뛰어나다. 

student_number가 필요한 이유: 실제 세계에서 학생을 식별하기 위한 nautral key이자 비즈니스 데이터이다. 

둘을 항상 같은 값으로 사용하지 않아도 되는 이유: id는 DB내부용, student_number는 외부 비즈니스용이다. 
```

## 3-5. 숫자처럼 보이는 학번을 문자열로 저장한 이유

```text
나의 설명: 현재 학번이 00으로 시작하는데, 숫자로 다루면 앞에 00이 사라질 수가 있다. 
숫자 모양이라고 해서 항상 숫자 타입이 적절한 것은 아니다. 
```

---

# 4. 테이블과 조회 결과는 다르다

## 4-1. 원본 테이블 행 수

```text
ch02_students 전체 행 수: 3
```

## 4-2. 일부 열만 조회

실행 SQL:
SELECT name, major
FROM ch02_students
ORDER BY id;

```sql
SELECT name, major
FROM ch02_students
ORDER BY id;
```

```text
원본 테이블의 열 수와 조회 결과의 열 수가 다른 이유: name, major 열만 지정해서 조회했기 때문
```

## 4-3. 조건을 적용한 조회

실행 SQL: 

```sql
SELECT id, student_number, name, major
FROM ch02_students
WHERE major = '컴퓨터공학'
ORDER BY id;
```

```text
원본 테이블 행 수: 3
조회 결과 행 수: 2
원본 테이블의 데이터가 삭제된 것인가?: 아니다.
그렇게 판단한 이유: 전공이 컴퓨터공학인 학생데이터만 조회하는데, 해당 행이 2개이다.
```

## 4-4. 정렬 결과 비교

```sql
SELECT id, name
FROM ch02_students
ORDER BY name ASC;

SELECT id, name
FROM ch02_students
ORDER BY name DESC;
```

```text
ASC 결과의 첫 학생: 김민지
DESC 결과의 첫 학생: 이준호

이 실험을 통해 ORDER BY에 대해 알게 된 점: order by는 해당 칼럼 기준으로 정렬한다. 
```

## 4-5. 증거 화면

권장 경로:

```text
assignments/chapter02/images/step04_result_set.png
```

`여기에 STEP 4 핵심 증거 화면을 삽입하세요.`

---

# 5. PK와 FK를 실제로 관찰

## 5-1. 정상 데이터의 관계 읽기

다음 SQL 결과를 보고 작성합니다.

```sql
SELECT
    e.id AS enrollment_id,
    s.name AS student_name,
    c.title AS course_title,
    e.status
FROM ch02_enrollments AS e
JOIN ch02_students AS s
    ON s.id = e.student_id
JOIN ch02_courses AS c
    ON c.id = e.course_id
ORDER BY e.id;
```

```text
한 행이 의미하는 것: 수강신청_id, 학생이름, 과목이름, 신청상태

같은 student_id가 여러 enrollment 행에서 반복될 수 있는 이유: 한 학생이 여러 과목을 신청할 수 있기 때문이다. 

같은 course_id가 여러 enrollment 행에서 반복될 수 있는 이유: 한 과목을 여러 학생이 수강신청할 수 있기 때문이다. 
```

## 5-2. 기본키 중복 오류 관찰

중복 PK 입력을 시도한 결과:

```text 
실행 성공 / 실패:
오류 메시지에서 확인한 핵심 단어:
왜 실패했다고 생각하는가:
```

## 5-3. 존재하지 않는 학생을 참조하는 FK 오류 관찰

존재하지 않는 `student_id`를 사용한 수강신청 입력 결과: SQL Error [23505]: 오류: 중복된 키 값이 "ch02_students_pkey" 고유 제약 조건을 위반함
  세부 정보: (id)=(1) 키가 이미 있습니다.


```text
실행 성공 / 실패: 실패
오류 메시지에서 확인한 핵심 단어: Pkey, 중복된 키 값이 고유 제약조건을 위반함
왜 실패했다고 생각하는가: ch02_students 테이블의 id행이 PK인데, id = 1 을 추가하려고 하니 PK가 중복되어 오류가 남 
```

## 5-4. PK와 FK의 차이 정리

```text
PK는 ____________자신의 테이블에서 행을 구분________ 하기 위한 키이다.

FK는 ________참조 대상 키와 연결__________ 하기 위한 키이다.

FK 값이 여러 행에서 반복될 수 있는 이유는
__________1:N 관계를 표현하기/참조하는 대상(PK)과 참조하는 주체(FK) 간의 1:N__________ 때문이다.
```

## 5-5. 증거 화면

권장 경로:

```text
assignments/chapter02/images/step05_pk_fk.png
```

> 오류 메시지는 전체 화면이 아니라 테이블명·constraint·참조 오류가 보이는 정도만 캡처합니다.

`여기에 STEP 5 핵심 증거 화면을 삽입하세요.`

---

# 6. 관계와 카디널리티를 자연어로 설명

현재 임시 데이터 기준으로 작성합니다.

```text
학생 한 명은 여러 수강신청을 가질 수 있는가?:

강의 한 개는 여러 수강신청을 가질 수 있는가?:

수강신청 한 건은 학생 몇 명을 참조하는가?:

수강신청 한 건은 강의 몇 개를 참조하는가?:
```

아래 구조를 완성합니다.

```text
students 1 ── ___N___ enrollments ___N___ ── 1 courses
```

### 학생과 강의가 N:M 관계라고 볼 수 있는 이유

```text
나의 설명: 학생 한 명은 여러 수강신청을 가질 수 있고, 강의 한 개는 여러 수강신청을 가질 수 있다. 수강신청 한 건은 학생 한 명을 참조하고, 강의 한 개를 참조한다. 
```

> 아직 0개 허용 여부, 필수 관계, 삭제 정책까지 확정하지 않습니다. 그런 규칙은 Chapter 05~06에서 다룹니다.

---

# 7. AI가 만든 테이블 구조 직접 검토

## 7-1. AI에게 묻기 전에 내가 먼저 찾은 문제

다음 구조를 보고 최소 4개를 적습니다.

```sql
CREATE TABLE student_courses (
    student_name VARCHAR(50),
    student_email VARCHAR(100),
    course_title VARCHAR(100),
    instructor_name VARCHAR(50)
);
```

```text
문제 1. 이 테이블의 한 행은 무엇을 의미하는가? - 학생 이름 / 학생 메일 / 강의 제목 / 교수 이름
문제 2. 각 행을 안정적으로 구분하는 PK가 있는가? - 없다. 
문제 3. 학생,강의,강사를 이름 문자열로만 연결해도 되는가? - 안된다. 중복되는 행이 생긴다면 문제가 생길 수 있다. 
문제 4. 서로 다른 종류의 현재 정보와 사건 정보가 섞여 있지 않은가? - 잘 모르겠음
```

## 7-2. AI 검토 요청 프롬프트

사용한 핵심 프롬프트를 기록합니다.

```나는 PostgreSQL과 데이터베이스를 처음 배우는 학생입니다.
아직 정규화와 ERD를 정식으로 배우기 전입니다.
다음 테이블 구조를 검토해 주세요.
CREATE TABLE student_courses (
    student_name VARCHAR(50),
    student_email VARCHAR(100),
    course_title VARCHAR(100),
    instructor_name VARCHAR(50)
);
완성된 정답 설계를 바로 만들어 주지 말고 다음 질문 중심으로 설명해 주세요.
1. 한 행의 의미가 명확한가?
2. PK 후보가 필요한가?
3. 내부 식별자와 업무 식별자를 구분할 필요가 있는가?
4. FK로 표현해야 할 관계 후보는 무엇인가?
5. 중복 저장 위험이 있는가?
6. 현재 요구사항만으로 결정할 수 없는 정책은 무엇인가?
확정되지 않은 업무 규칙은 임의로 결정하지 마세요.

## 7-3. AI 제안과 나의 판단

| AI의 지적 또는 제안 | 동의 / 수정 / 보류 | 나의 근거 |
| --- | --- | --- |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |

## 7-4. 본문과 대조한 항목

AI 설명 중 최소 하나를 `chapter02.md`와 비교합니다.

```text
AI가 설명한 내용:

본문에서 확인한 내용:

일치 / 부분 일치 / 수정 필요:

내가 최종적으로 이해한 내용:
```

## 7-5. 증거 화면

권장 경로:

```text
assignments/chapter02/images/step07_ai_review.png
```

`여기에 AI 검토 과정의 핵심 화면을 삽입하세요.`

---

# 8. Chapter 01의 개인 서비스 아이디어를 DB 용어로 다시 표현

Chapter 01에서 정한 개인 서비스 주제를 그대로 사용하거나 새 주제를 정해도 됩니다.

## 8-1. 서비스 기본 정보

```text
서비스 이름: 중고 거래
서비스 목적: 판매자는 물건을 올리고, 구매자와 판매자를 연결한다. 
```

## 8-2. PostgreSQL 구조 후보

```text
데이터베이스 이름 후보: ai_database_study
스키마 이름 후보: secondhand_transaction
```

> 아직 실제 데이터베이스나 스키마를 생성하지 않아도 됩니다.

## 8-3. 테이블 후보와 한 행 의미

최소 3개를 작성합니다.

| 테이블 후보 | 한 행의 의미 | 내부 ID 후보 | 업무 식별자 후보 |
| --- | --- | --- | --- |
| 판매자/구매자 | 사용자 한명의 정보 | user_id | email / 전화번호 |
| 거래 | 거래 한 건의 정보 | transaction_id | 결제번호 |
| 물건 | 중고 물품 게시글 1건의 정보 | item_id | 물품번호 |

## 8-4. FK 후보

```text
1. item.seller_id  → users.user_id
   이유: 한 판매자가 여러 물품을 올릴 수 있다. 물건 테이블이 판매자의 ID를 외래키로 참조한다. 

2. transactions.item_id → items.item_id
   이유: 하나의 물건에 대해 거래 사건이 발생하므로, 거래 테이블이 거래에 대해 알려주기 위해 물건의 ID를 외래키로 참조한다. 

3. transactions.buyer_id → users.user_id
   이유: 한 명의 구매자(users)가 여러 번의 거래(transactions)를 할 수 있으므로, 거래 테이블이 구매자의 ID를 외래키(FK)로 참조한다.
```

## 8-5. 자연어 관계 문장

```text
1. 모든 물건 게이글에는 '이 물건을 올린 판매자'가 누군지 반드시 기록되어 있어야 한다. 
2. 모든 거래 내역에는 '어떤 물건'을 거래했는지 반드시 기록되어 있어야 한다. 
3. 모든 거래 내역에는 '이 물건을 산 구매자(회원)'가 누군지 반드시 기록되어 있어야 한다. 
```

## 8-6. 아직 확정하지 않을 정책

```text
Q1.
Q2.
Q3.
```

---

# 9. AI를 개인 구조의 검토자로 사용

## 9-1. 사용한 프롬프트

```text

```

## 9-2. AI가 질문한 내용 중 유용했던 것

```text
1.
2.
3.
```

## 9-3. AI가 너무 빨리 결정한 내용 또는 내가 보류한 내용

```text
1.
2.
```

## 9-4. 검토 후 수정한 구조

| 수정 전 | 수정 후 | 수정 이유 |
| --- | --- | --- |
|  |  |  |
|  |  |  |
|  |  |  |

---

# 10. 최종 개념 정리

아래 문장을 본인의 말로 완성합니다.

```text
PostgreSQL은 데이터를 저장, 조회하고 설정되 규칙을 적용하는 DMBS 이다.

DBeaver 또는 psql은 DMBS에 명령을 보내고 결과를 보여주는 클라이언트 이다.

데이터베이스와 스키마의 차이는 데이터베이스는 데이터의 집합체이고, 스키마는 테이블 간의 규칙을 정의하고 관계를 보여주는 설계도 이다.

테이블 한 행은 쉽게 말하면 가로줄인데, 하나의 기록 이다. 

조회 결과가 원본 테이블과 다른 이유는 원본은 실제 저장된 데이터의 물리적 구조이고, 조회 결과는 사용자의 요구(SQL 명령)에 따라 필터링·가공되어 화면에 보여지는 가상의 결과(Result Set)이기 때문이다.

내부 식별자와 업무 식별자의 차이는 업무식별자는 현실에서 대상을 식별하기 위한 것이고, 내부 식별자는 DB내부에서 행을 구분하기 위한 것 이다.

PK는 기본키(고유키)인데, 특징으로는 중복이 되지 않으면서 not null이라는 특징을 갖는다.  이다.

FK는 외래키로서. 다른 테이블에 있는 키들을 참조해서 가져온다. 이다.
```

---

# 11. 이번 Chapter에서 새롭게 알게 된 점

최소 3개를 작성합니다.

```text
1.
2.
3.
```

## 아직 헷갈리는 내용

```text
1.
2.
```

## AI에게 다시 질문하고 싶은 내용

```text

```

---

# 12. 제출 전 자기 점검

- [ ] PostgreSQL에서 현재 database / schema / search_path를 확인했다.
- [ ] DBMS, database, schema, table을 구분해서 설명할 수 있다.
- [ ] TEMP TABLE 3개를 생성하고 직접 데이터를 조회했다.
- [ ] 각 테이블의 한 행 의미를 작성했다.
- [ ] 테이블과 조회 결과가 다르다는 것을 실제 SQL로 확인했다.
- [ ] `ORDER BY`를 사용하지 않으면 업무 순서를 가정하면 안 된다는 점을 이해했다.
- [ ] 내부 식별자와 업무 식별자의 차이를 설명할 수 있다.
- [ ] PK 중복 입력 실패를 직접 확인했다.
- [ ] 존재하지 않는 FK 참조 실패를 직접 확인했다.
- [ ] FK 값이 반복될 수 있는 이유를 설명할 수 있다.
- [ ] AI가 만든 테이블을 내가 먼저 검토했다.
- [ ] AI 설명 중 최소 하나를 본문과 대조했다.
- [ ] 개인 서비스의 테이블 후보를 3개 이상 작성했다.
- [ ] 개인 서비스의 FK 후보와 미확정 정책을 기록했다.
- [ ] 실제 비밀번호·API Key·민감한 접속 정보가 포함되지 않았는지 확인했다.
- [ ] 이미지 링크가 GitHub에서 정상적으로 보이는지 확인했다.

---

# 13. GitHub 제출 정보

답안 파일 권장 위치:

```text
assignments/chapter02/chapter02_answer.md
```

이미지 권장 위치:

```text
assignments/chapter02/images/
```

LMS 제출 URL 형식:

```text
https://github.com/<본인-GitHub-ID>/<본인-저장소>/blob/main/assignments/chapter02/chapter02_answer.md
```

## 최종 확인

- [ ] 위 URL을 로그아웃 상태 또는 다른 브라우저에서 열어도 확인 가능하다.
- [ ] Markdown이 정상 렌더링된다.
- [ ] 이미지가 깨지지 않는다.
- [ ] LMS에 교수자 템플릿 URL이 아니라 **내 답안 파일 URL**을 제출했다.