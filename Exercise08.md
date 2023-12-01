Exercise 8
===========

+ 실습목표: ﻿extension board(boostxl-edumkii)의 LCD를 API를 이용해 다뤄보고 LDC에 이름과 *을 출력한다. 
*은 extension board에 달려 있는 두개의 큰 버튼(P5.1, P3.5)를 눌러 위아래로 움직이게 할 수 있다.

> 코드:
> ------
> ```c
> #include <ti/devices/msp432p4xx/inc/msp.h>
> #include <ti/devices/msp432p4xx/driverlib/driverlib.h>
> #include <ti/grlib/grlib.h>
> #include "LcdDriver/Crystalfontz128x128_ST7735.h"
> #include "LcdDriver/msp432p4111_classic.h"
> 
> /* Graphic library context */
> Graphics_Context g_sContext;
> 
> /*
>  * Main function
>  */
> 
> #define INIT -1
> #define DOWN 0
> #define UP 1
> #define TH 50000
> 
> void main(void)
> {
>     int x=10, y=8;
>     int bs1state=INIT;
>     int bs1count=0;
>     int bs2state=INIT;
>     int bs2count=0;
> 
>     /* Initializes display */
>     Crystalfontz128x128_Init();
> 
>     /* Set default screen orientation */
>     Crystalfontz128x128_SetOrientation(LCD_ORIENTATION_UP);
> 
>     /* Initializes graphics context */
>     Graphics_initContext(&g_sContext, &g_sCrystalfontz128x128, &g_sCrystalfontz128x128_funcs);
>     Graphics_setForegroundColor(&g_sContext, GRAPHICS_COLOR_RED);
>     Graphics_setBackgroundColor(&g_sContext, GRAPHICS_COLOR_WHITE);
>     GrContextFontSet(&g_sContext, &g_sFontFixed6x8);
>     Graphics_clearDisplay(&g_sContext);
> 
>     /* Configuring S1 & S2 buttons in boostxl-edumkii */
>     P3->DIR &= ~BIT5;
>     P5->DIR &= ~BIT1;
>     P3->REN |= BIT5;
>     P5->REN |= BIT1;
>     P3->OUT |= BIT5;
>     P5->OUT |= BIT1;
> 
>     Graphics_drawString(&g_sContext, (int8_t *)"Jeon Heewon", AUTO_STRING_LENGTH, 32, 0, OPAQUE_TEXT);
>     Graphics_drawString(&g_sContext, (int8_t *)"*", 1, x*6, y*7, OPAQUE_TEXT);
> 
>     while(1)
>     {
>         while (!(P5->IN & BIT1)) {
>             bs1state=DOWN;
>             if ((++bs1count) == TH) {
>                 bs1count=0;
>                 Graphics_drawString(&g_sContext, (int8_t *)" ", 1, x*6, y*7, OPAQUE_TEXT);
>                 if (y>1) y--;
>                 Graphics_drawString(&g_sContext, (int8_t *)"*", 1, x*6, y*7, OPAQUE_TEXT);
>             }
>         }
>         bs1count=0;
>         if (P5->IN & BIT1){
>             if (bs1state==DOWN) {
>                 bs1count=0;
>                 Graphics_drawString(&g_sContext, (int8_t *)" ", 1, x*6, y*7, OPAQUE_TEXT);
>                 if (y>1) y--;
>                 Graphics_drawString(&g_sContext, (int8_t *)"*", 1, x*6, y*7, OPAQUE_TEXT);
>                 bs1state=UP;
>             }
>         }
> 
>         while (!(P3->IN & BIT5)) {
>              bs2state=DOWN;
>              if ((++bs2count) == TH) {
>                  bs2count=0;
>                  Graphics_drawString(&g_sContext, (int8_t *)" ", 1, x*6, y*7, OPAQUE_TEXT);
>                  if (y<17) y++;
>                  Graphics_drawString(&g_sContext, (int8_t *)"*", 1, x*6, y*7, OPAQUE_TEXT);
>              }
>          }
>         bs2count=0;
>          if (P3->IN & BIT5){
>              if (bs2state==DOWN) {
>                  bs2count=0;
>                  Graphics_drawString(&g_sContext, (int8_t *)" ", 1, x*6, y*7, OPAQUE_TEXT);
>                  if (y<17) y++;
>                  Graphics_drawString(&g_sContext, (int8_t *)"*", 1, x*6, y*7, OPAQUE_TEXT);
>                  bs2state=UP;
>              }
>          }
>     }
> }
> ```
+ 코드 설명: ﻿우선 그래픽 컨텍스트를 생성하고 디스플레이 드라이버와 컨텍스트를 모두 초기화한다. 이때 foreground는 빨간색, background는 흰색, 폰트 사이즈는 6*8로 설정했다. extension board의 두 버튼(P5.1, P3.5)을 사용할 수 있도록 저항은 풀업으로 하여 설정한다. Graphics_drawString함수(매개변수: 생성한 컨텍스트, 출력할 문자열, 문자열의 길이, x위치, y위치, 덮어쓰기 여부)를 이용해 맨 위 중앙에 이름과 초기 위치(중앙)에 *을 출력한다. 이때 LCD는 128*128픽셀로 이루어져 있고, 폰트 사이즈가 6*8이므로 LCD에 가로로 들어갈 수 있는 최대 문자수 즉 가로행은 21, 세로열은 16이다. 따라서 x위치는 21의 절반 정도인 10(정확히는10.5이지만, 눈대중으로는 비슷하므로 대략)에 6을 곱해주었고, y위치는 16의 절반인 8에 7(폰트 사이즈에 따르면 8이지만, 이쪽이 더 자연스럽다)을 곱해주어 중앙에 출력되도록 했다. 이 *위치의 가로행과 세로열 값을 저장할 변수를 각각 x,y로 선언하여 *의 위치를 움직일 때 사용하도록 한다.   
버튼을 누르면 위아래로 움직이게 하기 위해 Round-Robin architecture를 사용한다. 각 버튼이 눌렸을 때의 state를 Down, 그렇지 않을 때의 state를 Up이라고 할 때 각각의 버튼에 대해 다음과 같은 로직이 작동한다: 버튼이 눌리면 Down state가 되고, 눌려진 상태로 일정 시간이 지날 때마다(반복문을 일정 횟수 돌 때마다) *을 한 칸씩(y±1) 움직인다(Graphics_drawString함수를 통해 원래 위치에 공백을 출력해 지우고 y를 증가 혹은 감소시킨 위치에 다시 *을 출력한다, 각 x위치와 y위치는 변수 x,y에 6,7을 곱한 값으로 한다). 버튼이 떼지면, *를 한 칸 움직이고 Up state가 된다. 즉 버튼을 누른 즉시 떼면 *은 한 칸만 움직인다. *이 움직이는 방향은 위쪽 버튼(P5.1)을 누르면 위쪽, 아래쪽 버튼(P3.5)을 누르면 아래쪽이다. *이 맨 위에 써진 이름을 덮지 않고, 아래쪽으로는 화면 밖으로 벗어나지 않도록 제한을 두는데, 맨 위의 이름은 세로로 8픽셀이므로 어림잡아 변수 y값이 1보다 작아지지 않도록 하고 아래쪽으로는 화면 세로 크기인128을 7로 나눈 값에서 어림잡은 17보다 커지지 않도록 한다.

