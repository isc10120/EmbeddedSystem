Exercise3
=========
+ 실습목표: 메인보드의 오른쪽 led에 색이 G, GB, B, RB, R, RG, RGB 순서대로 번갈아 나오게 한다. 비트 flipping을 연습한다.
```c
#include "msp.h"


/**
 * main.c
 */
void main(void)
{
	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer

	P2->DIR=7;

	// 순서: 2 6 4 5 1 3 7
	int i;
	while(1){
	    P2->OUT = BIT1;       // 2 (G)
	    for(i=0;i<100000;i++);
	    P2->OUT |= BIT2;      // 6 (GB)
	    for(i=0;i<100000;i++);
	    P2->OUT &= ~BIT1;     // 4 (B)
	    for(i=0;i<100000;i++);
	    P2->OUT |= BIT0;      // 5 (RB)
	    for(i=0;i<100000;i++);
	    P2->OUT &= ~BIT2;     // 1 (R)
	    for(i=0;i<100000;i++);
	    P2->OUT |= BIT1;      // 3 (RG)
	    for(i=0;i<100000;i++);
	    P2->OUT |= BIT2;      // 7 (RGB)
	    for(i=0;i<100000;i++);
	}
}
```
