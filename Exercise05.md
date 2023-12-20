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
  <img src="https://github.com/isc10120/EmbeddedSystem/assets/112538487/b402b190-c9a5-443a-93a7-78124c6bf942" width="500" height="500"/>   
P1.1 Pressed State와 P1.4 Pressed State에서는 항상 왼쪽 LED에 빨간 불이 켜져 있도록 하고, 다른
State에 진입하면 꺼지도록 한다.   
Action1은 오른쪽 LED의 색이 R이면 B로, B이면 G로, G이면 R로 바꾸는 동작이고,   
Action2는 오른쪽 LED의 색이 R이면 G로, G이면 B로, B이면 R로 바꾸는 동작이다.   
이때 output 레지스터에서는 값을 읽어올 수 없으므로 오른쪽 LED의 색을 저장하는 변수를 따로
선언해주었다.   
각 버튼이 눌렸는지 여부는 define한 S1BUT와 S2BUT를 통해 알 수 있도록 했는데, Pull-up 레지스터를
사용했으므로 P1 input 레지스터의 각 핀이 0, 즉 low일때 버튼이 눌렸다고 판단한다.


