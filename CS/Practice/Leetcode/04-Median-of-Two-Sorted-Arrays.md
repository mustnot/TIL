## 04. Median of Two Sorted Arrays

>  There are two sorted arrays **nums1** and **nums2** of size m and n respectively.
>
>  Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
>
>  You may assume **nums1** and **nums2** cannot be both empty.

두 개의 오름차순으로 정렬된 리스트가 존재하고, 두 리스트를 합쳐서 보았을 때의 중앙값을 리턴하는 문제이다. 여기서 키포인트는 두 리스트가 정렬되었지만 연결되어 정렬된 것이 아닌 서로 다른 값으로 정렬되어 있어서 다른 리스트에 리스트 원소보다 더 작거나 큰 값이 있을 수 있다.

<br>



### Using Sorted 

사실 `Python` 내장함수인 `Sorted`를 사용하지 않고 풀고 싶었는데 실행 시간으로 인해서 `sorted`를 사용함 (`quick sort` 를 써봐야겠다. 다음에는...)

* 시간 복잡도 : O(N log N) 이다. 길이, 인덱스 계산 외에는 정렬 로직만 포함됨
* 공간 복잡도 : O(N) 으로 새로 정렬된 리스트인 `array`

```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        n, m = len(nums1), len(nums2)
        if (n + m) % 2 == 1:
            index1 = index2 = int((n + m + 1) / 2) - 1
        else:
            index2 = int((n + m) / 2)
            index1 = index2 - 1
        array = sorted(nums1 + nums2)
        return (array[index1] + array[index2]) / 2
```