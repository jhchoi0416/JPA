# JPA (java Persistence API)
1. JPA 란?
   - JPA 는 자바 ORM 기술에 대한 API 표준 명세다.
2. 왜 JPA를 사용해야 하는가?
   1. 생산성
      - SQL을 작성하고 JDBC API를 사용하는 지루하고 반복적인 작업은 JPA가 대신 처리해준다.
      - JPA에서는 CREATE TABLE 같은 DDL 문을 자동으로 생성해주는 기능도 있다.
   2. 유지보수
      - 개발자가 작성해야 했던 SQL과 JDBC API 코드를 JPA가 대신 처리해주므로 유지보수해야 하는 코드 수가 줄어든다.
   3. 패러다임 불일치 해결
   4. 성능
   5. 데이터 접근 추상화와 벤더 독립성
      - 애플리케이션이 특정 데이터베이스 기술에 종속되지 않는다.
      - 만약 데이터베이스 변경하면 JPA에게 다른 데이터베이스를 사용한다고 알려주기만 하면 된다.
   6. 표준
      
3. 즉시 로딩 vs 지연 로딩
    - 지연 로딩: 실제 객체를 사용하는 시점까지 데이터베이스 조회를 미룬다고 해서 지연 로딩이라 한다.
    - JPA는 연관된 객체를 즉시 함께 조회할지 아니면 실제 사용되는 시점에 지연해서 조회 할 지를 간단한 설정으로 정의할 수 있다.
4. 비교
    - 객체는 동일성 비교와 동등성 비교라는 두 가지 비교 방법이 존재한다.
        - 동일성 비교는 == 비교다. 객체 인스턴스의 주소 값을 비교한다.
        - 동등성 비교는 equals() 메소드를 사용해서 객체 내부의 값을 비교한다.
        
        ```java
        int idx = 10;
        Member m1 = dao.selectMemberByIdx(idx);
        Member m2 = dao.selectMemberByIdx(idx);
        
        m1 == m2; 
        // 결과: 불일치
        // m1, m2 같은 데이터베이스 row에서 조회했지만, 
        // 객체 측면에서는 다른 인스턴스기 때문에 다르다고 판단.
        ```
        
    - JPA와 비교
        - JPA는 같은 트랜잭션인 경우 같은 객체가 조회 되는 것을 보장한다.
        
        ```java
        int idx = 10;
        Member m1 = jpa.find(Member.class, idx);
        Member m2 = jpa.find(Member.class, idx);
        
        m1 == m2;
        // 결과: 일치
        ```
5. JPA 에서 제공하는 매핑 어노테이션
   1. @Entity
      - 해당 클래스를 테이블과 매핑한다고 JAP에게 알려준다.
      - @Entity가 사용된 클래스를 엔티티 클래스라 한다.
   2. @Table
      - 엔티티 클래스에 매핑할 테이블 정보를 알려준다.
      - 해당 어노테이션을 생략하면 클래스 이름을 테이블 이름으로 매핑한다
   3. @Id
      - 엔티티 클래스의 필드를 테이블의 기본 키(primary key)에 매핑한다.
      - 해당 어노테이션을 사용한 필드를 식별자 필드라 한다.
   4. @Column
      - 필드를 컬럼에 매핑한다.
   5. 매핑 정보가 없는 필드
      - 매핑 어노테이션을 생략하면 필드명을 사용해서 컬럼명으로 매핑한다.
      - 만약 대소문자를 구분하는 데이터베이스를 사용하면 @Column(name="AGE")처럼 명시적으로 매핑해야 한다.
      <br>
   ```
   @Entity
   @Table(name="MEMBER")
   public class Member {
      @Id
      @Column(name = "ID")
      private String id;

      @Column(name = "name")
      private String name;

      // 매핑 정보가 없는 필드
      private int age;
   }
   ```        
6. JPA 제공하는 API
    1. 저장
        - persist()
        - 객체를 데이터베이스에 저장
        - JPA가 객체와 매핑 정보를 보고 적절한 INSERT SQL을 생성해서 데이터베이스에 전달
    2. 조회
        - find()
        - 객체 하나를 데이터베이스에서 조회한다.
        - JPA는 객체와 매핑 정보를 보고 적절한 SELECT SQL을 생성해서 데이터베이스에 전달하고 그 결과로 객체를 생성해서 반환한다.
    3. 수정
        - find()
        - object.setXXX()
        - 별도의 수정 메소드를 제공하지 않는다.
        - 대신에 객체를 조회해서 값을 변경하면 트랜잭션을 커밋할 때 데이터베이스에 적절한 UPDATE SQL이 전달된다.
        
        ```java
        Member m = jpa.find(Member.class, id);
        m.setName("name update"); // 수정
        ```
    4. 삭제
       - remove()
       - 객체를 데이터베이스에서 삭제     
    5. 연관된 객체 조회
        - find()
        - object.getXXXX()
        - ex)
        
        ```java
        Member m = jpa.find(Member.class, id);
        Team t = member.getTeam(); // 연관된 객체 조회
        ```
7. JPQL
   - createQuery(JPQL, 반환 타입)
   - JPA는 SQL을 추상화한 JPQL 이라는 객체지향 쿼리 언어를 제공한다.
   - JPQL은 SQL과 문법이 거의 유사하다.
      - ex) SELECT, FROM, WHERE, GROUP BY...
   - SQL과 차이점
      - JPQL은 엔티티 객체를 대상으로 쿼리한다. 쉽게 이야기해서 클래스와 필드를 대상으로 쿼리한다.
      - SQL은 데이터베이스 테이블을 대상으로 쿼리한다.
   - JPQL은 데이터베이스 테이블을 전혀 알지 못한다.
   ```
   public List<Member> getMemberList(EntityManager em) {
      // from Member는 회원 엔티티 객체
      // MEMBER 
      TypedQuery<Member> query = em.createQuery("select m from Member m", Member.class);
      return query.getResultList();
   ```

        

