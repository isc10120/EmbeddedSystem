Exercise 3
==========   
+ 실습목표: 메인보드의 1번 led는 빨간색, 2번 led는 RGB색이 번갈아 나타나도록 동시에 깜빡거리게 만든다.
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
	// 구조체 사용하지 말고 직접 메모리에 접근

	int i;
	unsigned char j = 1;
  	//output register는 읽어올 수 없으므로 j를 사용

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
