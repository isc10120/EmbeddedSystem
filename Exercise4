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
