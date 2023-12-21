+ 실습목표:    
> 코드:
> -----
> ```c
> #include "msp.h"
> 
> /**
>  * main.c
>  */
> 
> int color = 1;
> 
> void PORT1_IRQHandler(){
>     if(P1->IFG & BIT1){
>         if(color<4)
>             color*=2;
>         else color=1;
> 
>         P1->IFG &= ~BIT1;
>     }
>     if(P1->IFG & BIT4){
>         if(color==1)
>             color=4;
>         else color/=2;
> 
>         P1->IFG &= ~BIT4;
>     }
>     P2->OUT = color;
> }
> 
> void main(void)
> {
> 	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer
> 
> 	P1->DIR = ~(BIT1|BIT4);
> 	P1->REN = BIT1|BIT4;
> 	P1->OUT = BIT1|BIT4;
> 
> 	P2->DIR = BIT0|BIT1|BIT2;
> 
> 	P1->IES = BIT1|BIT4; // high-to-low (1)
> 	P1->IFG = 0;         // flag clear
> 	P1->IE = BIT1|BIT4;  // Interrupt enable
> 
> 	NVIC->ISER[1] = 1 << 3;   // PORT1_IRQHandler enable
> 
> 	P2->OUT = color;
> 	while(1);
> }
> 
> ```
