Exercise 6
==========   

+ 실습목표: Systick을 이용하여 메인보드 왼쪽 led에 빨간 불이 정해진 시간만큼 켜졌다 꺼지는 것을 반복하도록 한다. 이 시간은 3초부터 0.5초씩 줄어들며 마지막에는 0.5초로 9번 깜박이고 다시 3초로 돌아간다.
  
> 코드:
> ----
> ```c
> #include "msp.h"
> 
> /**
>  * main.c
>  */
> 
> void SysTick_Wait(uint32_t n){
>     SysTick->LOAD=n-1;
>     SysTick->VAL=0;
>     while((SysTick->CTRL & 0x0010000)==0);
> }
> void main(void)
> {
> 	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer
> 
> 	SysTick->CTRL=5;
> 	P1->DIR=1;
> 
> 	while(1){
> 	    int i=9000000; // 3 sec ( Clock Rate가 3 MHz이므로  1초에 3백만, 3초면 9백만 )
> 
> 	    while(i>1500000){  
> 	        P1->OUT=1;
> 	        SysTick_Wait(i);
> 	        P1->OUT=0;
> 	        SysTick_Wait(i);
> 	        i-=1500000;  // 0.5 sec
> 	    }
> 	    int j=9;
> 	    while(j--){ 
> 	        P1->OUT=1;
> 	        SysTick_Wait(i);  // i = 1500000 (0.5 sec)
> 	        P1->OUT=0;
> 	        SysTick_Wait(i);
> 	    }
> 
> 	}
> }
> ```
+ 설명:   
  Systick은 일종의 타이머로, 소스로 사용하는 clock의 clock rate에 따라 그 속도가 달라진다. 최대 24bit의 값을 STRVR(Systick->LOAD로 접근)에 집어넣으면 그 값은 STCVR(Systick->VAL로 접근)이 0이 되었을 때 STCVR로 들어가 소스 clock의 1 clock cycle마다 1씩 감소하며, 0이 되었을 때 STCSR(Systick->CTRL로 접근)의 16번째 비트(COUNTFLAG Field)가 1로 설정된다. 따라서 n-1을 넣으면 n clock cycle 후 Count Flag가 1이 된다. Systick을 사용하려면 STCSR의 0번째 비트(ENABLE Field)를 1로 설정하고, 2번째 비트(CLKSOURCE Field)를 1로 설정하여 core clock을 소스로 사용하도록 해줘야 한다.   
  이 Systick을 이용해 주어진 시간만큼 기다리는 함수 Systick_Wait를 만들어 사용했다. 함수의 로직은 다음과 같다: 매개변수로 n을 받아 n-1을 Systick에 로드해주고 카운트를 초기화(VAL=0)한 후, Count Flag가 1이 될때까지 반복문으로 딜레이를 준다. 이때 Systick->CTRL과 0x0010000을 bit AND 연산한 값이 0이 아니면 Count Flag가 1임을 이용한다.   
  사용하는 clock의 clock rate가 3Mhz이므로, Systick의 속도는 1초당 3,000,000이다. 따라서 3초를 세려면 9,000,000 - 1을 Systick에 로드해줘야 하고, 0.5초씩 줄이기 위해서는 로드하는 값을 1,500,000씩 줄여줘야 한다. 해당 사실과 위 함수를 이용해 불을 켜고, 3초를 기다리고, 다시 불을 끄고, 3초를 기다리는 것을 0.5초씩 줄여가며 반복한다. 마지막 0.5초가 되면 9번 반복하고 다시 3초로 돌아가 반복해준다.
  
  > *참고 :  
  > STCSR: SysTick Control and Status Register   
  > STCVR: SysTick Current Value Register   
  > STRVR: SysTick Reload Value Register*   
