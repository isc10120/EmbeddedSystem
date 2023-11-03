Exercise 2
==========   

+ 실습목표: 메인보드 led에 빨간 불이 점점 더 빨리 깜빡거리게 만든다.
> ## 코드:
>
> ```c
> #include "msp.h"
> /**
>  * main.c
>  */
> void main(void)
> {
> 	 WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer
> 	 P1->DIR |=BIT0;
>	 int i=100000;
>	 while(1){
>	     P1->OUT=BIT0;
>	     int j;
>	     for(j=0;j<i;j++)
>	         ;
>	     P1->OUT=0;
>	     for(j=0;j<i;j++)
>	         ;
>	     if(i<20000) continue;
>	     i-=5000;
>	 }
> }
> ```
   
+ 설명:
  watchdog timer(WDT)는 MCU의 operation을 감시하여 의도치 않은 행동을 하거나 너무 오래 지속될 때 reset신호를 보내므로, 멈춰놓는다.구조체로 선언된 P1을 이용하여 Port1의 Direction에 접근한다. 해당 바이트의 LSB에 1을 주어 P1.0(led빨강)을 output으로 설정한다. 이때 BIT0(0000 0001)과 bit OR연산하여 나머지 비트는 건드리지 않도록 했다. P1 구조체의 OUT의 LSB에 마찬가지로 1을 주면 output으로 설정된 빨간색 led에 불이 켜지고, 0을 주면 다시 꺼진다. 반복문을 이용하여 딜레이를 주어 led가 켜졌다 꺼졌다를 반복하게 했다. 이 딜레이를 점점 줄여 더 빠르게 깜빡거리게 했고 일정 딜레이에 도달하면 딜레이 줄이기를 멈춰 일정 속도로 계속 깜빡거릴 수 있게 했다.


