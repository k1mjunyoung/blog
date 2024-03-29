---
title: "JPA 대량 데이터 한번에 UPDATE하기"
description: 
date: 2023-03-29 
update:
tags:
  - jpa
series: 
---
# Bulk update

JPA로 List를 받아와서 수정 후 `saveAll()` 하는 기존 로직에서 hibernate 로그가 수없이 뜨는 걸 발견, JPA가 데이터 수만큼 건별로 Update하기 때문

```java
List<Tbl080Block> findByRejectNum(String rejectNum);
```

벌크 업데이트를 위해선 `@Modifying` 어노테이션을 사용해야 한다.

> SpringDataJPA가 해당 리포지토리 메소드가 SELECT문인지 UPDATE문인지 알아야 Return Type을 결정하는데, 아직은 내부적으로 Query만 보고 판단을 못해서, 명시를 해줘야 Return type을 결정하기 때문입니다.
> 

```java
@Modifying
@Query(nativeQuery = true, value = "UPDATE TBL_080_BLOCK SET EXP_DT = :nowDt WHERE REJECT_NUM = :rejectNum")
int updateExpDtByRejectNum(@Param("rejectNum") String rejectNum, @Param("nowDt")LocalDateTime nowDt);
```

벌크 업데이트를 할 때, 주의사항이 있다.

1차 캐시라고 불리는 **영속성 컨텍스트** 때문인데, **벌크 업데이트는 단일 업데이트와 다르게 영속성 컨텍스트에 있는 엔티티를 변경하지 않는다.**

> 내부적인 동작은 아래와 같이 동작합니다.
> 
> 1. 영속성 컨텍스트에 엔터티 2개를 추가 + 쓰기 지연 SQL 저장소에 INSERT문 저장
> 2. 영속성 컨텍스트를 flush(JPQL 실행 때문) -> **쓰기 지연 SQL 저장소에만 UPDATE문 저장**
> 3. 영속성 컨텍스트에서 엔터티 조회
> 4. Update문이 적용되지 않은 값을 반환

해결방법은 벌크 업데이트로 인해 변경된 값을 참조해야 한다면, 영속성 컨텍스트를 DB에 반영하고 영속성 컨텍스트를 비워야 한다. 그렇게 되면, 해당 값을 참조할 때 영속성 컨텍스트가 아니라 DB에 직접 접근하게 되어서 반영된(업데이트된) 값을 가져올 수 있다.

`@Modifying` 의 `clearAutomatically` 옵션을 사용해야 한다.

```java
@Modifying(clearAutomatically = true)
@Query(nativeQuery = true, value = "UPDATE TBL_080_BLOCK SET EXP_DT = :nowDt WHERE REJECT_NUM = :rejectNum")
int updateExpDtByRejectNum(@Param("rejectNum") String rejectNum, @Param("nowDt")LocalDateTime nowDt);
```

위와 같이 네이티브 쿼리를 적용하니 또 다른 에러가 발생했는데, Transaction 관련 에러였다.

```bash
javax.persistence.TransactionRequiredException:Executing an update/delete query 
```

`@Transactional` 어노테이션을 붙이니 해결되었는데 트랜잭션에 대한 공부는 추후에..

```java
@Transactional
@Modifying(clearAutomatically = true)
@Query(nativeQuery = true, value = "UPDATE TBL_080_BLOCK SET EXP_DT = :nowDt WHERE REJECT_NUM = :rejectNum")
int updateExpDtByRejectNum(@Param("rejectNum") String rejectNum, @Param("nowDt")LocalDateTime nowDt);
```

[참고] [https://jaehoney.tistory.com/151](https://jaehoney.tistory.com/151)