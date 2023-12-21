Exercise 7
==========

+ 실습목표: 인터럽트를 사용하여 P2 LED의 색이 S1버튼(P1.1)을 누를 때마다 R->G->B->R->... 로, S2 버튼(P1.4)을
  누를 때마다 R->B->G->R... 로 바뀌게 한다. P2 LED의 초기값은 빨간색이다.
  
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

   + 설명: 버튼들을 사용하기 위해 P1->DIR에서 해당하는 핀(1,4)을 0으로 설정하고, P1->REN과 P1->OUT에서
해당하는 핀을 1로 설정해 Pull-up 저항을 사용하도록 한다. 또 P2->DIR의 해당하는 핀(0-2)을 1로
설정하여 P2 LED의 RGB색 모두 사용할 수 있도록 한다.
Cortex-M4프로세서에 내장된 IC(Interrupt Controller)인 NVIC(Nested Vectored Interrupt Controller)는
인터럽트가 발생하면, 메모리의 인터럽트 벡터 테이블-ISR(Interrupt Service Routine)함수의 포인터
배열-에서 해당 인터럽트에 맞는 ISR함수의 위치를 찾아 실행한다. 각 ISR에 매핑된 인터럽트 핸들러는
NVIC->ISER 배열을 통해 enable할 수 있다. ISER[0]은 0-31, ISER[1]은 32-63으로 각각
32비트씩이므로, P1 핸들러에 해당하는 35를 enable하려면 ISER[1]의 3번째 비트를 1로 설정해야 한다.
또한 인터럽트 자체를 사용하기 위해 P1->IE에서 핀1,4에 1을 주어 해당하는 인터럽트(S1, S2버튼)를
enable해야 한다. 그리고 P1->IES의 핀1,4에 1을 주어 high-to-low(falling edge, 풀업을 사용했으므로
버튼이 눌렸을 때 high-to-low가 된다)일때 flag가 1이 되도록 설정해주고, P1->IFG로 flag에 접근하여
클리어해 준다.
인터럽트 핸들러를 오버라이딩해 사용하는데, 이때 이름을 정확하게 적어주어야 한다. P1에 해당하는
핸들러의 이름은 PORT1_IRQHandler로, S1버튼이 눌렸을 때 flag(P1->IFG)의 1번 핀이, S2버튼이
눌렸을 때 flag의 4번 핀이 1이 된다는 것을 이용해 각각 눌릴 때마다 P2 LED의 색이 R->G->B->G... ,
R->B->G->R... 순서로 바뀌도록 해주고, 해당하는 flag를 다시 0으로 바꿔주었다. 이때
output레지스터는 읽어올 수 없으므로 전역변수 color를 사용했다.

     > *참고:   
     > IE Register: Interrupt Enable Register   
     > IES Register: Interrupt Edge Select Register   
     > IFG Register: Interrupt Flag Register   
     > ISER# Register: IRQ ## to ## Set Enable Register*

