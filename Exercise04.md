Exercise 4
=========
+ 실습목표: 메인보드의 오른쪽 led에 색이 G, GB, B, RB, R, RG, RGB 순서대로 번갈아 나오게 한다. 비트 연산을 연습한다.

> ## 코드
> ```c
> #include "msp.h"
> /**
> * main.c
> */
> void main(void)
> {
> 	WDT_A->CTL = WDT_A_CTL_PW | WDT_A_CTL_HOLD;		// stop watchdog timer
> 
>  	P2->DIR=7;
>
>	// 순서: 2 6 4 5 1 3 7
>	int i;
>	while(1){
>	    P2->OUT = BIT1;       // 2 (G)
>	    for(i=0;i<100000;i++);
>	    P2->OUT |= BIT2;      // 6 (GB)
>	    for(i=0;i<100000;i++);
>	    P2->OUT &= ~BIT1;     // 4 (B)
>	    for(i=0;i<100000;i++);
>	    P2->OUT |= BIT0;      // 5 (RB)
>	    for(i=0;i<100000;i++);
>	    P2->OUT &= ~BIT2;     // 1 (R)
>	    for(i=0;i<100000;i++);
>	    P2->OUT |= BIT1;      // 3 (RG)
>	    for(i=0;i<100000;i++);
>	    P2->OUT |= BIT2;      // 7 (RGB)
>	    for(i=0;i<100000;i++);
>   }
> }
> ```

+ 설명:   
  ccs는 편의를 위해 미리 정의된 매크로와 데이터 타입들을 제공한다. 이는 msp.h의 선언부(Declaration)에 들어간 후 다시 사용하는 보드에 해당하는 헤더 파일의 선언부에 들어가면 확인할 수 있다.
  여기서 내가 쓰는 보드에 해당하는 msp432P4111.h에 들어가보면 다음과 같은 코드를 확인할 수 있다.   
