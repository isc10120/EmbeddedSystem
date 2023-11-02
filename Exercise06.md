```c
#include "msp.h"

/**
 * main.c
 */

void SysTick_Wait(uint32_t n){
    SysTick->LOAD=n-1;
    SysTick->VAL=0;
    while((SysTick->CTRL & 0x0010000)==0);
}
void main(void)
{
	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer

	SysTick->CTRL=5;
	P1->DIR=1;

	while(1){
	    int i=9000000; // 3 sec ( Clock Rate가 3 MHz이므로  1초에 3백만, 3초면 9백만 )

	    while(i>1500000){  
	        P1->OUT=1;
	        SysTick_Wait(i);
	        P1->OUT=0;
	        SysTick_Wait(i);
	        i-=1500000;  // 0.5 sec
	    }
	    int j=9;
	    while(j--){  // 0.5 sec
	        P1->OUT=1;
	        SysTick_Wait(i);  // i = 1500000
	        P1->OUT=0;
	        SysTick_Wait(i);
	    }

	}
}
```
