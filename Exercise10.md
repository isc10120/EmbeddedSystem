```c
/* Driver configuration */
#include <ti/devices/msp432p4xx/inc/msp.h>
#include <ti/devices/msp432p4xx/driverlib/driverlib.h>
#include <ti/drivers/Board.h>

/* RTOS header files */
#include <FreeRTOS.h>
#include <task.h>
#include <semphr.h>

/* function prototypes */
void Task1( void *pvParameters );
void Task2( void *pvParameters );
void Task3( void *pvParameters );
void Task4( void *pvParameters );

TaskHandle_t task1Handle, task2Handle, task3Handle, task4Handle;

int MAXpriority = 1;

void main(void)
{
    /* Configuring S1 & S2 buttons / Red LED & RGB LED in mainboard */
    P1->DIR = BIT0;
    P1->DIR &= ~(BIT1|BIT4);
    P2->DIR |= 1<<2 | 1<< 1 | 1<<0;
    P1->REN |= (BIT1|BIT4);
    P1->OUT |= (BIT1|BIT4);
    P2->OUT = 0x00;

    xTaskCreate( Task1,                   /* The function that implements the task. */
                 "Task 1",                /* The text name assigned to the task - for debug only as it is not used by the kernel. */
                 configMINIMAL_STACK_SIZE,/* The size of the stack to allocate to the task. */
                 ( void * ) 0,            /* The parameter passed to the task - just to check the functionality. */
                 MAXpriority,                      /* The priority assigned to the task. */
                 &task1Handle );                  /* The task handle is not required, so NULL is passed. */
    xTaskCreate(Task2, "Task 2", configMINIMAL_STACK_SIZE, (void *)0, 0, &task2Handle);
    xTaskCreate(Task3, "Task 3", configMINIMAL_STACK_SIZE, (void *)0, 0, &task3Handle);
    xTaskCreate(Task4, "Task 4", configMINIMAL_STACK_SIZE, (void *)0, 0, &task4Handle);

    /* Start the tasks and timer running. */
    vTaskStartScheduler();
    while (1)
    {
    }
}

void Task1( void *pvParameters )
{
    int i;
    while (1)
    {
       P1->OUT |= BIT0;
       for (i=0;i<100000;i++);
       P1->OUT &= ~BIT0;
       for (i=0;i<100000;i++);
       vTaskPrioritySet(task2Handle, ++MAXpriority);
    }
}

void Task2( void *pvParameters )
{
    int i;
    while (1)
    {
       P2->OUT = 1;
       for (i=0;i<100000;i++);
       P2->OUT = 0x00;
       for (i=0;i<100000;i++);
       vTaskPrioritySet(task3Handle, ++MAXpriority);
    }
}

void Task3( void *pvParameters )
{
    int i;
    while (1)
    {
       P2->OUT = 1<<1;
       for (i=0;i<100000;i++);
       P2->OUT = 0x00;
       for (i=0;i<100000;i++);
       vTaskPrioritySet(task4Handle, ++MAXpriority);
    }
}

void Task4( void *pvParameters )
{
    int i;
    while (1)
    {
       P2->OUT = 1<<2;
       for (i=0;i<100000;i++);
       P2->OUT = 0x00;
       for (i=0;i<100000;i++);
       MAXpriority = 1;
       vTaskPrioritySet(task1Handle, MAXpriority);
       vTaskPrioritySet(task2Handle, 0);
       vTaskPrioritySet(task3Handle, 0);
       vTaskPrioritySet(task4Handle, 0);
    }
}

```
