---
title:  "🔎 이진탐색과 분할정복"
description: "이진"
date: 2024-01-10
update: 
tags:
  - algorithm
series: "알고리즘 정복하기"
---

## 이진탐색이란

이진 탐색은 업다운 게임과 비슷하다. 찾고자 하는 정답이 포함된 범위 중 가운데를 검사하고, 정답과 비교하여 절반의 범위를 제외한다.

### 장점

선형 탐색*(배열이나 리스트의 처음부터 하나씩 찾는 방법)*은 원소 개수에 비례하는 `O(N)`의 시간복잡도를 갖는데, 이진 탐색은 공간의 크기를 `N/2^x`로 줄여나가므로 `O(logN)`의 시간복잡도를 갖는다.

### 조건

이진 탐색을 적용하려면 배열이나 리스트가 **정렬되어 있어야 한다.**

## 탐색 효율 높이기

### 분할 정복

분할 정복은 업다운 게임과 같이 탐색 공간을 특정 기준에 따라 나누고, 나눈 각 탐색 공간에서 탐색을 이어 나가는 방법이다.

1. 범위 찾기
2. 검사 진행하기
    
    [start, end]로 표기된 범위에서 범위 내 속한 원소 개수는 `end - start`
    
    탐색 공간이 남아있지 않을 때까지 탐색하려면 `end-start`가 양수일 때 탐색을 계속 반복해야 한다. `end-start > 0` 이므로 `end > start`를 조건으로 하는 반복문으로 탐색을 반복한다.
    
    ```java
    while (end > start) { 
    	// 범위의 중간 검사
    }
    ```
    
3. 중간 값 비교하기
    
    ```java
    int mid = (start + end) / 2;
    int value = arr[mid];
    ```
    
    중간 값을 구했으면 이 값과 찾는 값의 대소를 비교하고, 그에 따라 범위를 적절히 조정한다.
    
    ```java
    if (value == target) {
    	return mid;
    } else if (value > target) {
      // Down
    } else {
    	// Up
    }
    ```
    
    중간값이 찾는 값과 같다면 해당 값의 인덱스인 mid를 반환한다.
    
    `value`가 더 크다면 정답은 더 작은 범위에 있으므로 작은 범위에서 탐색을 이어나가고 `value`가 더 작다면 정답은 더 큰 범위에 있으므로 큰 범위에서 탐색을 이어나가야 한다.
    
    주의해야 할 점은 `value > target`은 범위를 작은 쪽으로 좁혀야 하고, `value < target`은 범위를 더 큰 쪽으로 좁혀야 한다.
    
    ```java
    if (value == target) {
    	return mid;
    } else if (value > target) {
    	end = mid;
    } else {
    	start = mid + 1;
    }
    ```
    

전체 코드

```java
private static int binarySearch(int[] arr, int target) {
	int strat = 0;
	int end = arr.length;

	while (end > start) {
		int mid = (start + end) / 2;
		int value = arr[mid];

		if (value == target) {
			return mid;
		} else if (value > target) {
			end = mid;
		} else { 
			start = mid + 1;
		}
	}

	return -1;
}
```

### 정렬 기준 정하기

> 이진 탐색을 진행하려면 배열이 정렬되어 있어야 하고, 대부분의 경우 우리가 직접 데이터를 정렬해주어야 한다.

1. 정렬방식 선택하기
    
    이진 탐색을 위해서는 중간값과 정답의 대소를 명확히 구분할 수 있어야 하고, 대소 비교를 하여 정답이 속한 더 작은 범위를 정확히 파악할 수 있어야 한다. 예를 들어, 모든 자릿수의 합이 9인 숫자를 찾는데 오름차순으로 정렬하고 이진 탐색을 적용할 수는 없다.
    
    따라서 이진 탐색을 적용하려면 문제에서 요구하는 조건을 정확히 파악하고, 이에 따른 대소 비교를 구현하여 데이터를 정렬한 후 진행해야 한다.
    

1. 정렬 규칙 찾기
    
    이진 탐색 문제의 대부분은 배열이나 리스트를 주고 원소를 찾기보다는 큰 범위의 정답 후보 중 문제 조건에 맞는 정답을 찾아낼 때가 많다. 이때는 문제에서 요구하는 조건의 검사 경과가 정답 후보의 값에 따라 정렬된 상태가 맞는지 확인해 보아야 한다.
    
    이진 탐색은 정확한 값을 찾는 데도 사용되지만, 정답 조건을 만족하는 값 중 가장 큰 값 또는 가장 작은 값을 찾는 데에도 많이 이용된다.
    
    정답 조건을 만족하는 값 중 가장 큰 값이나 가장 작은 값을 쉽게 찾으려면 범위 좁히기, 범위 표기법 등을 고민해야 한다.
    

## 자바의 이진탐색 메서드

자바에서는 배열과 리스트에 적용할 수 있는 이진 탐색 메서드를 제공한다.

```java
Arrays.binarySearch() // 배열
Collections.binarySearch() // 리스트
```

**단, 탐색 대상 배열과 리스트는 항상 정렬된 상태여야 한다.**

따라서 배열이나 리스트가 정렬되어 있다고 가정할 수 없다면 `Arrays.sort()`나 `Collections.sort()` 메서드로 배열이나 리스트를 정렬한 후 이진 탐색을 진행해야 한다.

```java
int[] array = new int[] {1, 4, 6, 7, 8, 10, 13, 17};
List<Integer> list = List.of(1, 4, 6, 7, 8, 10, 13, 17);\

int arrayIndex = Arrays.binarySearch(array, 8);
int listIndex = Collections.binarySearch(list, 8);

System.out.println(arrayIndex); // 4
System.out.println(listIndex); // 4
```

위 코드에서 숫자 `8`의 인덱스를 찾기 위해 `Arrays.binarySearch()`와 `Collections.binarySearch()` 메서드로 이진 탐색을 진행하면 해당 원소의 인덱스인 `4`를 반환한다.

만약 찾고자 하는 값이 없다면 어떤 값을 반환할까?

```java
int[] array = new int[] {1, 4, 6, 7, 8, 10, 13, 17};
List<Integer> list = List.of(1, 4, 6, 7, 8, 10, 13, 17);\

int arrayIndex = Arrays.binarySearch(array, 11);
int listIndex = Collections.binarySearch(list, 11);

System.out.println(arrayIndex); // -7
System.out.println(listIndex); // -7
```

위 예시처럼 없는 원소를 검색하면 음수가 반환되는데. 이 값을 이용하면 검색하려는 원소가 배열이나 리스트에서 어느 위치에 들어가야 하는지 구할 수 있다. **음수 반환값을 양수로 변환하고 1을 빼면 원소가 들어갈 위치가 된다.**

이진 탐색 메서드는 직접 이진 탐색을 구현하지 않아도 된다는 점에서는 매우 유용하다. 정렬된 배열이나 리스트에서 하나의 원소를 찾아야 할 때 강력한 효율성을 보여준다.


[참고] 프로그래머스 코딩 테스트 문제 풀이 전략: 자바편