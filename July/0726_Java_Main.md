
# main 함수는?

~~~java
public static void main(String[] args) {
	
}
~~~

public : 접근제어자 | 모든 객체에서 다 접근이 가능
static : 이 함수가 정적 함수인지를 의미 static으로 함수 또는 클래스를 선언할경우 해당 객체는 자바가 컴파일 되는 순간 정의가 된다. 
* static 객체에서 static이 아닌 객체를 호출하는 것은 불가능 -> static이 먼저 정의가 되기 때문에 아직 정의되지 않은 객체를 호출할 수 없다. 

처음부터 JVM에서 main 메소드를 호출하기 때문에 프로그램 실행시점부터 고정된 주소값을 가지고 메모리에 할당되는 static 함수가 필요하다.

