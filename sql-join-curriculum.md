# 📚 SQL JOIN 수업 커리큘럼 (최적화된 재정리)

## 🎯 수업 목표
- Oracle과 ANSI 표준 조인 문법을 체계적으로 학습
- 내부조인과 외부조인의 차이점을 명확히 이해
- 실무에서 올바른 조인 방식을 선택할 수 있는 능력 배양

---

## 📋 Phase별 진행 순서

### **[Phase 1] 조인의 기초 원리 (30분)**
> **목표: "조인이 뭔지 근본적으로 이해하기"**

#### 1️⃣ **실습 8-1: 카티션 곱 (CROSS JOIN)**
- **조인 구분**: 크로스 조인
- **문법**: Oracle (WHERE 절 없음)
- **핵심 포인트**: 
  - 조인의 시작점 - 모든 가능한 조합
  - 14×4=56행 확인 → "이건 문제가 있네?"
- **SQL 코드**:
```sql
SELECT *
FROM EMP, DEPT
ORDER BY EMPNO;
-- 결과: 56행 (14 × 4)
```
- **교육 메시지**: "조인 조건이 없으면 이렇게 됩니다!"
- ⚠️ **경고**: 실무에서 절대 사용 금지!

---

### **[Phase 2] 내부조인 - Oracle 방식 (50분)**
> **목표: "전통적인 Oracle 조인 완벽 마스터"**

#### 2️⃣ **실습 8-2: 등가 조인 (Oracle INNER JOIN)**
- **조인 구분**: 내부조인 (등가)
- **문법**: Oracle (WHERE 조인조건)
- **핵심 포인트**: 
  - 조건을 주니 의미있는 14행만 남았다!
  - Primary Key - Foreign Key 관계 활용
- **SQL 코드**:
```sql
SELECT *
FROM EMP, DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO
ORDER BY EMPNO;
-- 결과: 14행 (각 사원당 1행)
```

#### 3️⃣ **실습 8-3: 테이블 별칭 (Oracle INNER JOIN)**
- **조인 구분**: 내부조인 (등가)
- **문법**: Oracle + 별칭
- **핵심 포인트**: 
  - 별칭으로 코드를 간결하게
  - E(Employee), D(Department)
- **SQL 코드**:
```sql
SELECT *
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO
ORDER BY EMPNO;
```

#### 4️⃣ **실습 8-4: 컬럼명 충돌 오류 (문제 체험)**
- **조인 구분**: 내부조인 (오류 케이스)
- **문법**: Oracle
- **핵심 포인트**: 
  - DEPTNO 애매함 오류 체험
  - ORA-00918: column ambiguously defined
- **SQL 코드**:
```sql
SELECT EMPNO, ENAME, DEPTNO, DNAME, LOC  -- 오류 발생!
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO;
```
- **교훈**: "왜 테이블 지정이 필요한지 깨달음"

#### 5️⃣ **실습 8-5: 명확한 컬럼 지정 (Oracle INNER JOIN)**
- **조인 구분**: 내부조인 (등가)
- **문법**: Oracle (완성형)
- **핵심 포인트**: 
  - 모든 컬럼에 테이블 지정
  - 오류 해결 완료
- **SQL 코드**:
```sql
SELECT E.EMPNO, E.ENAME, D.DEPTNO, D.DNAME, D.LOC
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO
ORDER BY D.DEPTNO, E.EMPNO;
```

#### 6️⃣ **실습 8-6: 조인 + 필터 조건 (Oracle INNER JOIN)**
- **조인 구분**: 내부조인 (등가)
- **문법**: Oracle
- **핵심 포인트**: 
  - 조인 조건: E.DEPTNO = D.DEPTNO
  - 필터 조건: AND SAL >= 3000
  - 두 조건의 역할 구분
- **SQL 코드**:
```sql
SELECT E.EMPNO, E.ENAME, E.SAL, D.DEPTNO, D.DNAME, D.LOC
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO  -- 조인 조건
  AND SAL >= 3000;          -- 필터 조건
-- 결과: 3행 (KING, FORD, SCOTT)
```

#### 7️⃣ **실습 8-7: 비등가 조인 (Oracle NON-EQUI JOIN)**
- **조인 구분**: 내부조인 (비등가)
- **문법**: Oracle
- **핵심 포인트**: 
  - 등호(=) 대신 BETWEEN 사용
  - 범위 조건으로 매칭
- **SQL 코드**:
```sql
SELECT *
FROM EMP E, SALGRADE S
WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL;
-- 급여가 등급 범위에 속하는지 확인
```

#### 8️⃣ **실습 8-8: 자체 조인 (Oracle SELF JOIN)**
- **조인 구분**: 내부조인 (자체)
- **문법**: Oracle
- **핵심 포인트**: 
  - 같은 테이블을 두 번 사용
  - 계층 구조 표현 (사원-매니저)
- **SQL 코드**:
```sql
SELECT E1.EMPNO, E1.ENAME, E1.MGR,
       E2.EMPNO AS MGR_EMPNO,
       E2.ENAME AS MGR_ENAME
FROM EMP E1, EMP E2
WHERE E1.MGR = E2.EMPNO;
-- 결과: 13행 (KING 제외 - MGR이 NULL)
```

---

### **[Phase 3] 외부조인 - Oracle 방식 (30분)**
> **목표: "NULL 처리가 필요한 외부조인 이해"**

#### 9️⃣ **실습 8-9: LEFT OUTER JOIN (Oracle)**
- **조인 구분**: 외부조인 (LEFT)
- **문법**: Oracle (+)
- **핵심 포인트**: 
  - KING도 포함시키려면 (+) 사용
  - (+)는 오른쪽에 붙임
  - 왼쪽 테이블 전체 보존
- **SQL 코드**:
```sql
SELECT E1.EMPNO, E1.ENAME, E1.MGR,
       E2.EMPNO AS MGR_EMPNO,
       E2.ENAME AS MGR_ENAME
FROM EMP E1, EMP E2
WHERE E1.MGR = E2.EMPNO(+)  -- E2쪽에 (+)
ORDER BY E1.EMPNO;
-- 결과: 14행 (KING 포함, MGR_ENAME이 NULL)
```

#### 🔟 **실습 8-10: RIGHT OUTER JOIN (Oracle)**
- **조인 구분**: 외부조인 (RIGHT)
- **문법**: Oracle (+)
- **핵심 포인트**: 
  - 부하없는 사원 찾기
  - (+)는 왼쪽에 붙임
  - 오른쪽 테이블 전체 보존
- **SQL 코드**:
```sql
SELECT E1.EMPNO, E1.ENAME, E1.MGR,
       E2.EMPNO AS MGR_EMPNO,
       E2.ENAME AS MGR_ENAME
FROM EMP E1, EMP E2
WHERE E1.MGR(+) = E2.EMPNO  -- E1쪽에 (+)
ORDER BY E1.EMPNO, MGR_EMPNO;
-- 부하직원이 없는 사원들 확인 가능
```

---

### **[Phase 4] ANSI 표준 전환 - 내부조인 (40분)**
> **목표: "이제부터 ANSI 표준으로 전환!"**

#### 1️⃣1️⃣ **실습 8-13: JOIN ON (ANSI INNER JOIN)** ⭐⭐⭐⭐⭐
- **조인 구분**: 내부조인 (등가)
- **문법**: ANSI 표준
- **핵심 포인트**: 
  - 가장 권장하는 방식!
  - 유연성, 명확성, 표준
  - 모든 조건 표현 가능
- **SQL 코드**:
```sql
SELECT E.EMPNO, E.ENAME, E.JOB, E.SAL,
       E.DEPTNO, D.DNAME, D.LOC
FROM EMP E JOIN DEPT D 
ON (E.DEPTNO = D.DEPTNO)
WHERE SAL <= 3000
ORDER BY E.DEPTNO, EMPNO;
```
- **장점**:
  - 복잡한 조건 가능
  - 서브쿼리 사용 가능
  - 함수 사용 가능
  - 별칭 자유롭게 사용

#### 1️⃣2️⃣ **실습 8-12: JOIN USING (ANSI INNER JOIN)**
- **조인 구분**: 내부조인 (등가)
- **문법**: ANSI 표준
- **핵심 포인트**: 
  - 명시적 컬럼 지정
  - 별칭 사용 불가 (제약사항)
- **SQL 코드**:
```sql
SELECT E.EMPNO, E.ENAME, E.SAL,
       DEPTNO,  -- 별칭 사용 불가!
       D.DNAME, D.LOC
FROM EMP E JOIN DEPT D 
USING (DEPTNO)
WHERE SAL >= 3000
ORDER BY DEPTNO, E.EMPNO;
```

#### 1️⃣3️⃣ **실습 8-11: NATURAL JOIN (위험!)** ⛔
- **조인 구분**: 내부조인 (자동)
- **문법**: ANSI 표준
- **핵심 포인트**: 
  - 절대 사용 금지!
  - 조인 조건 자동 생성 (위험)
  - 테이블 구조 변경시 장애 발생
- **SQL 코드**:
```sql
SELECT E.EMPNO, E.ENAME,
       DEPTNO,  -- 공통 컬럼
       D.DNAME, D.LOC
FROM EMP E NATURAL JOIN DEPT D
ORDER BY DEPTNO, E.EMPNO;
```
- **실제 장애 사례**:
  - 6개월 후 CREATE_DATE 컬럼 추가
  - 자동으로 조인 조건 변경됨
  - 95% 데이터 조회 안됨 → 서비스 장애!

---

### **[Phase 5] ANSI 표준 - 외부조인 (40분)**
> **목표: "ANSI 외부조인으로 완성"**

#### 1️⃣4️⃣ **실습 8-14: LEFT OUTER JOIN (ANSI)**
- **조인 구분**: 외부조인 (LEFT)
- **문법**: ANSI 표준
- **핵심 포인트**: 
  - Oracle (+)보다 명확
  - 의도가 분명히 드러남
- **SQL 코드**:
```sql
SELECT E1.EMPNO, E1.ENAME, E1.MGR,
       E2.EMPNO AS MGR_EMPNO,
       E2.ENAME AS MGR_ENAME
FROM EMP E1 
LEFT OUTER JOIN EMP E2 
ON (E1.MGR = E2.EMPNO)
ORDER BY E1.EMPNO;
-- 결과: 14행 (KING 포함)
```
- **Oracle vs ANSI 비교**:
  - Oracle: `WHERE E1.MGR = E2.EMPNO(+)`
  - ANSI: `LEFT JOIN ... ON`
  - ANSI가 더 직관적!

#### 1️⃣5️⃣ **실습 8-15: RIGHT OUTER JOIN (ANSI)**
- **조인 구분**: 외부조인 (RIGHT)
- **문법**: ANSI 표준
- **핵심 포인트**: 
  - 오른쪽 기준 (실무에서 잘 안씀)
  - LEFT JOIN으로 변환 권장
- **SQL 코드**:
```sql
SELECT E1.EMPNO, E1.ENAME, E1.MGR,
       E2.EMPNO AS MGR_EMPNO,
       E2.ENAME AS MGR_ENAME
FROM EMP E1 
RIGHT OUTER JOIN EMP E2 
ON (E1.MGR = E2.EMPNO)
ORDER BY E1.EMPNO, MGR_EMPNO;
```
- **팁**: `E1 RIGHT JOIN E2` = `E2 LEFT JOIN E1`

#### 1️⃣6️⃣ **실습 8-16: FULL OUTER JOIN (ANSI)**
- **조인 구분**: 외부조인 (FULL)
- **문법**: ANSI 표준 (Oracle 불가)
- **핵심 포인트**: 
  - 양쪽 모두 NULL 허용
  - LEFT + RIGHT 합집합
  - Oracle (+)로는 불가능
- **SQL 코드**:
```sql
SELECT E1.EMPNO, E1.ENAME, E1.MGR,
       E2.EMPNO AS MGR_EMPNO,
       E2.ENAME AS MGR_ENAME
FROM EMP E1 
FULL OUTER JOIN EMP E2 
ON (E1.MGR = E2.EMPNO)
ORDER BY E1.EMPNO;
```
- **결과 패턴**:
  1. 매칭: 부하-매니저 관계
  2. LEFT ONLY: 매니저 없음 (KING)
  3. RIGHT ONLY: 부하 없음

---

## 🎯 Phase별 핵심 메시지

| Phase | 주제 | 핵심 메시지 |
|-------|------|------------|
| Phase 1 | 기초 | "조인은 원래 모든 조합을 만드는 것" |
| Phase 2 | Oracle 내부조인 | "WHERE로 조건 주면 교집합만 남는다" |
| Phase 3 | Oracle 외부조인 | "(+) 기호로 NULL도 포함" |
| Phase 4 | ANSI 내부조인 | "이제 표준으로 바꿔보자, JOIN ON이 최고!" |
| Phase 5 | ANSI 외부조인 | "LEFT/RIGHT/FULL 명확히 표현" |

---

## 📊 수업 진행 체크리스트

### 각 Phase 시작 전 설명
```sql
-- Phase 1: "조인의 원리를 이해합시다"
-- Phase 2: "Oracle 방식 내부조인 (교집합)"
-- Phase 3: "Oracle 방식 외부조인 (NULL 포함)"  
-- Phase 4: "ANSI 표준 내부조인 (표준 전환)"
-- Phase 5: "ANSI 표준 외부조인 (완성)"
```

### 각 실습 후 확인
```sql
-- 결과 행 수 확인
SELECT COUNT(*) FROM (실습 쿼리);

-- 데이터 확인
SELECT COUNT(*) FROM EMP;     -- 14
SELECT COUNT(*) FROM DEPT;    -- 4
SELECT COUNT(*) FROM SALGRADE; -- 5
```

---

## 💡 전환점 강조 포인트

### Oracle → ANSI 전환 시점 (Phase 3→4)
- "지금까지 Oracle 방식을 배웠습니다"
- "이제 모든 DB에서 쓸 수 있는 ANSI 표준을 배웁니다"
- "면접에서는 ANSI를 쓰세요!"

### 내부조인 → 외부조인 전환 시점
- Phase 2 → Phase 3: "이제 NULL도 보여줘야 할 때"
- Phase 4 → Phase 5: "ANSI로 외부조인 완성"

---

## 🏆 최종 정리 (10분)

### JOIN 방식 비교표

| 구분 | Oracle | ANSI | 실무 추천도 |
|------|--------|------|------------|
| **내부조인** | `WHERE A.ID = B.ID` | `JOIN ON` | ⭐⭐⭐⭐⭐ |
| **LEFT** | `WHERE A.ID = B.ID(+)` | `LEFT JOIN` | ⭐⭐⭐⭐⭐ |
| **RIGHT** | `WHERE A.ID(+) = B.ID` | `RIGHT JOIN` | ⭐⭐⭐ |
| **FULL** | 불가능 | `FULL JOIN` | ⭐⭐ |
| **NATURAL** | 없음 | `NATURAL JOIN` | ⛔ |

### 핵심 규칙
1. ✅ **항상 JOIN ON 사용**
2. ✅ **테이블 별칭 필수**
3. ✅ **조인 조건과 필터 조건 구분**
4. ⛔ **NATURAL JOIN 절대 금지**
5. ⭐ **LEFT JOIN 선호 (RIGHT 대신)**

---

## 📝 수업 후 과제

### 기초 과제
1. 각 부서별 사원 수는?
2. 급여가 평균 이상인 사원과 부서 정보는?
3. 매니저가 없는 사원은 누구?

### 심화 과제
1. 급여가 자신의 매니저보다 높은 사원은?
2. 부하직원이 3명 이상인 매니저는?
3. 모든 사원의 급여 등급과 부서 정보를 한 번에 조회

### 실무 시나리오
1. "신입사원 교육 대상자 찾기" (입사 1년 미만 + 부서 정보)
2. "팀장 승진 대상자" (부하직원 있는 사원 중 급여 3000 이상)
3. "조직 개편 분석" (부서별 인원 + 평균 급여 + 최고/최저 급여)

---

## 🎓 수업 진행 팁

### 시작할 때
- "엑셀 VLOOKUP 아시죠? SQL JOIN이 바로 그겁니다!"
- 실습 환경 확인 (테이블 데이터 개수)

### 진행 중
- 각 실습 후 결과 행 수 확인
- 오류 발생시 함께 해결
- 실무 사례 연결

### 마무리
- Phase별 핵심 정리
- 실무 권장사항 강조
- Q&A 시간

---

## ❓ 예상 Q&A

1. **Q: Oracle (+)와 ANSI 중 뭘 써야 하나요?**
   - A: ANSI 표준 사용 권장 (이식성, 명확성)

2. **Q: RIGHT JOIN은 왜 잘 안 쓰나요?**
   - A: LEFT JOIN이 더 직관적, 테이블 순서 바꿔서 LEFT 사용

3. **Q: NATURAL JOIN은 왜 위험한가요?**
   - A: 테이블 구조 변경시 자동으로 조인 조건 변경 → 장애 위험

4. **Q: 조인 성능을 높이려면?**
   - A: 조인 컬럼에 인덱스, 작은 테이블 먼저 조인, EXPLAIN PLAN 활용

5. **Q: 3개 이상 테이블 조인은?**
   - A: 순차적으로 조인, 작은 결과셋부터 조인

---

## 📌 기억해야 할 한 문장

> **"JOIN ON을 기본으로, NATURAL JOIN은 절대 금지, 외부조인은 LEFT 선호!"**

---

*이 커리큘럼으로 취준생들이 SQL JOIN을 완벽히 마스터할 수 있습니다!* 💪