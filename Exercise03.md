Exercise 3
==========   
+ 실습목표: 메인보드의 1번 led는 빨간색 불, 동시에 2번 led는 RGB색 불이 번갈아 깜빡거리게 만든다.
```c
#include "msp.h"
/**
 * main.c
 */
void main(void)
{
	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;  // stop watchdog timer
	volatile unsigned char *p1_dir= (unsigned char*)(0x40004C04);
	volatile unsigned char *p2_dir= (unsigned char*)(0x40004C05);
	volatile unsigned char *p1_out= (unsigned char*)(0x40004C02);
	volatile unsigned char *p2_out= (unsigned char*)(0x40004C03);

	*p1_dir = 0x01;
	*p2_dir = 0x07;

	// P1->DIR |=BIT0;
	// P2->DIR |=7;
	// 구조체 사용하지 말고 직접 메모리 값을 입력해서 사용

	int i;
	unsigned char j = 1;
  	// output register는 읽어올 수 없으므로 j를 사용

	while(1){
	    *p1_out = BIT0;
	    *p2_out = j;
	    for(i=0; i<100000; i++);
	    *p1_out = 0;
	    *p2_out = 0;
	    for(i=0; i<100000; i++);
	    if(j==4) j = 1;
	    else j *= 2;
	}
}
```
+ 설명:   
  unsigned char * 자료형을 이용해 P1, P2의 direction과 output의 주소를 가리키는 변수를 만들어
volatile(컴파일러가 해당 변수를 최적화 대상에서 제외함)로 선언해주었다. (참고: GPIO memory map의 base address는 0x4000_4C00이고, 각각 P1(Port1)과 P2(Port2)의 direction의 오프셋은 04h(h: hexadecimal), 05h, output의 오프셋은 02h, 03h이다.) P1 direction의 lsb를 1로, P2
direction의 lsb부터 3개 비트를 1로 설정하여 각각 led의 빨간색, RGB색 output을 사용할 수 있게
한다. 이때 P2 direction에는 7을 주는 방법으로 3개 비트를 한번에 1로 설정해주었다.
그 다음 각각 output레지스터의 lsb에 1을 주어 빨간색 불이 들어오게 하고, 반복문을 사용해 딜레이를
준 후 0을 주어 꺼지게 하고 다시 딜레이를 줬다. 이 과정을 무한 반복하게 했는데, P2의 output은 따로
선언한 unsigned char변수를 통해 한번 돌 때마다 2를 곱해주고 4가 되면 1로 초기화하여 빨간색,
초록색, 파란색이 번갈아 나타날 수 있도록 했다. 변수를 따로 선언한 이유는 output 레지스터에서는
값을 읽어올 수 없어 현재 무슨 색인지 알 수 없기 때문이다.

