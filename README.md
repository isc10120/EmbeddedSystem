## Exercise 2
+ 사용한 메인보드: MSP-EXP432P4111 (ARM Cortex-M4)
+ 사용한 툴: Code Composer Studio   

```c
#include "msp.h"
/**
 * main.c
 */
void main(void)
{
	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer
	P1->DIR |=BIT0;
	int i=100000;
	while(1){
	    P1->OUT=BIT0;
	    int j;
	    for(j=0;j<i;j++)
	        ;
	    P1->OUT=0;
	    for(j=0;j<i;j++)
	        ;
	    if(i<20000) continue;
	    i-=5000;
	}
}
```

###### watchdog timer(WTD)는 MCU의 operation을 감시하여 의도치 않은 행동을 하거나 너무 오래 지속될 때 reset신호를 보내므로, 멈춰놓는다.
###### 구조체로 선언된 P1을 이용하여 Port1의 Direction에 접근한다. 해당 바이트의 msb에 1을 주면 Port1의 빨간색 led를 활성화시키게 된다.
###### BIT0(0000 0001)과 bit OR연산하여 나머지 비트는 건드리지 않도록 했다. 
###### 반복문을 이용하여 딜레이를 주어 led가 켜졌다 꺼졌다를 반복하게 했다. 
###### 이 딜레이를 점점 줄여 더 빠르게 깜빡거리게 했고 일정 딜레이에 도달하면 딜레이를 줄이기를 멈춰 일정 속도로 계속 깜빡거릴 수 있게 했다.


