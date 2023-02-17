# JPA (java Persistence API)
- JPA 는 자바 ORM 기술에 대한 API 표준 명세다.
- JPA 제공하는 API 사용
    1. 저장 기능
        - persist()
        - 객체를 데이터베이스에 저장
        - JPA가 객체와 매핑 정보를 보고 적절한 INSERT SQL을 생성해서 데이터베이스에 전달
    2. 조회 기능
        - find()
        - 객체 하나를 데이터베이스에서 조회한다.
        - JPA는 객체와 매핑 정보를 보고 적절한 SELECT SQL을 생성해서 데이터베이스에 전달하고 그 결과로 객체를 생성해서 반환한다.
    3. 수정 기능
        - find()
        - object.setXXX()
        - 별도의 수정 메소드를 제공하지 않는다.
        - 대신에 객체를 조회해서 값을 변경하면 트랜잭션을 커밋할 때 데이터베이스에 적절한 UPDATE SQL이 전달된다.
        
        ```java
        Member m = jpa.find(Member.class, id);
        m.setName("name update"); // 수정
        ```
        
    4. 연관된 객체 조회
        - find()
        - object.getXXXX()
        - ex)
        
        ```java
        Member m = jpa.find(Member.class, id);
        Team t = member.getTeam(); // 연관된 객체 조회
        ```
        

- 즉시 로딩 vs 지연 로딩
    - 지연 로딩: 실제 객체를 사용하는 시점까지 데이터베이스 조회를 미룬다고 해서 지연 로딩이라 한다.
    - JPA는 연관된 객체를 즉시 함께 조회할지 아니면 실제 사용되는 시점에 지연해서 조회 할 지를 간단한 설정으로 정의할 수 있다.
- 비교
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
