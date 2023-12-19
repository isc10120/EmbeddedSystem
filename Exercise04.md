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
  오른쪽 led 인 Port2 의 RGB 색을 모두 사용할 수 있도록 구조체로 미리 선언된 P2 의 DIR 에 7 을 주어
0000 0111 으로 설정한다. 그다음 OUT 에 BIT1 을 주어 G(green)색 불이 켜지도록 하고 반복문으로
딜레이를 준 뒤, BIT2 와 비트 OR 연산을 하여 GB 색으로 바뀌도록 한다. 또 딜레이를 준 뒤엔
~BIT1(1111 1101)과 비트 AND 연산하여 B 색으로 바꿔준다. 이런 식으로 비트연산을 통해 계속 값을
바꿔 목표한 대로 색이 순서대로 나오게 한다. 이 과정을 반복문으로 무한반복해준다.
