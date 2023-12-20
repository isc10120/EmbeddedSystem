Exercise 5
==========
+ 실습목표: 메인보드의 오른쪽 버튼을 누를 때마다 오른쪽 led의 색이 RGB 순서로 바뀌게 하고, 왼쪽 버튼을 누를
때마다 BGR순서로 바뀌게 한다. 이때, 버튼을 누르고 있는 동안에는 왼쪽 led가 빨간색으로 켜지도록
한다.   

> 코드:
> -----
> ```c
> #include "msp.h"
> #define S1BUT ((P1->IN & (1<<1))==0x00)
> #define S2BUT ((P1->IN & (1<<4))==0x00)
> 
> /**
>  * main.c
>  */
> void main(void)
> {
> 	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer
> 
> 	P1->DIR = ~(1<<1|1<<4);   // set 1, 4 as input, set 0 as output
> 	P1->REN = 1<<1|1<<4;  // enable resisters
> 	P1->OUT = 1<<4|1<<1;  // select pullup
> 
> 	P2->DIR=7;
> 	int color = 0, s1but_bs = 0, s2but_bs = 0;
> 	P2->OUT=1<<color;
> 
> 	while(1){
> 	    if(S1BUT){
> 	        s1but_bs=1;
> 	        P1->OUT |= 1;
> 	    }
> 	    else{
> 	        if(s1but_bs){
> 	            P1->OUT &= ~1;
> 	            if(color==0) color=2;
> 	            else color--;
> 	            P2->OUT = 1<<color;
> 	            s1but_bs = 0;
> 	        }
> 	    }
> 	    if(S2BUT){
> 	        s2but_bs=1;
> 	        P1->OUT |= 1;
> 	    }
> 	    else{
> 	        if(s2but_bs){
> 	            P1->OUT &= ~1;
> 	            if(color==2) color=0;
> 	            else color++;
> 	            P2->OUT = 1<<color;
> 	            s2but_bs = 0;
> 	        }
> 	    }
> 	}
> 
> }
> ```

+ 설명: 다음 FSM(Finite State Machine)과 같이 동작하도록 코드를 작성했다.
