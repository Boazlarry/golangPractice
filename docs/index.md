# Go 언어가 온당 live 강의 노트

___
# OT & 18장 (7/5 수) 슬라이스1

## OT
  마케터 골든래빗 조수현님
  진행자 공공식님
  넥슨, 네오위즈, EA캐나다
  비전공자 13년차 개발자
  캐나다 거주중

  교재 안내 -> Tucker의 Go 언어 프로그래밍
  - 1단계는 프로그래밍 기본적인 내용을 다루고
  - 2단계는 Go만의 특징에 대해서 자세히 알아보는 영역
  Go를 Go답게 더 효율적으로 사용하는 방법 배우기

## 슬라이스
Go에서 가장많이 사용하는 구조. 동적 배열 타입

정적 -> static -> compile time, build time
동적 -> dynamic -> runtime

상수 const는 static으로 고정. 바뀔수 없음
변수 var은 초기값을 갖지만 실행도중 바뀔 수 있음

동적배열은 array의 size가 바뀔 수 있음
[]리터럴은 상숫값이 들어가야함
C++의 vector, Java의 Array List, python의 slice, Javascript는 동적배열이 기본

slice 선언
go는 선언을 해야함. var slice []int와 같이(size는 비우고 요소타입 적기)
[][]int와 같은 타입도 가능(다차원 배열) =>메모리관리는 어떻게?
:= 선언 대입문을 사용해도 됨
예제
```go
package main
import "fmt"
func main() {
  var slice []int
  if len(slice) == 0 {
    fmt.Println("slice is empty", slice) 
  }
  slice[1] = 10
  fmt.Println(slice)
}
```
=> 빈 슬라이스를 출력하면 어떤 예외가 발생하나요, 혹은 null 을 return하나요?

[a:b]와 같이 대입하면 a위치에 b를 대입함

주의
[...]int {1,2,3}과 같이 하면 뒤의 선언 값에 따라 배열의 사이즈가 생성되므로 배열이 됨

Make()를 이용한 초기화
make는 내장함수. slice, map 타입을 만들때 사용
var slice = make([]int, 3)의 경우 3개의 요소를 갖고 있는 slice 반환\
make함수에 인자를 3개 전달하면 각각 포인터, Len, Cap이 됨 => make에 첫번째 인자로 전달하는 포인터를 이미 슬라이스로 할당한 포인터로 입력하면 어떻게 되나요? : 포인터가 아닌 타입을 적는것. 컴파일 에러남

슬라이스 순회
```go

var slice = []int{1,2,3}
for i := 0 < i < len(slice); i++ {}
for i, v := range slcie { }
```
슬라이스에 요소 추가
slice2:= append(slice, 4) -> 새로 슬라이스를 만들어 추가함, 기존 슬라이스는 그대로 둠 => 기존 슬라이스의 메모리 해제는 어떻게?, 추가하는 요소의 type이 다르면 어떤 에러가? : 강타입이라 변환불가, 타입변환을 해야함


슬라이스 동작 원리
type SliceHeader struct {
  Data uintptr //실제 배열을 가리키는 포인터
  Len int //요소 개수
  Cap int //실제 배열의 길이
}

사실 슬라이스는 go에서 제공하는 배열을 가리키는 포인터 타입 => 정확한 길이를 알 경우 slice가 아닌 배열을 쓰는게 성능적으로 유의미하게 좋은건가요
-> 18.4번 예제
```go

package main
import "fmt"
func changeArray(array2 [5]int) {
  array2[2] = 200
}
func changeSlice(slice2 []int) {
  slice2[2] = 200
}
func main() {
  array := [5]int{1,2,3,4,5}
  slice := []int{1,2,3,4,5}
  changeArray(array)
  changeSlice(slice)
  
  fmt.Println(array)
  fmt.Println(slice)
}
```
출력 결과 -> [1,2,3,4,5] [1,2,200,4,5] C나 C++했다면 array가 바뀔거라 생각하겠지만 go는 다름
go의 특징중 하나가 일관성. 어떤 상황에서도 똑같이 동작함
함수를 호출하면 인자는 rvalue로 동작함 call by value
슬라이스의 크기는 24byte로 고정됨

append 동작 원리
중요
슬라이스에 요소를 추가한 새로운 슬라이스를 반환
- 기존 슬라이스가 바뀔 수도 있고 아닐 수도 있음
- 빈공간이 충분하면 빈공간에 요소 추가, 아닐 경우 새로운 배열 할당 후 복사, 요소 추가
- 충분할 경우 기존 슬라이스 그대로

___
# 19장 (7/7 월) 슬라이스 2
