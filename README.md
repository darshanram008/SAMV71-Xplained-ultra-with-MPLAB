# spi
To perform SPI communication between SAMv71 and arduino
SAM as master
#include <stddef.h>                     // Defines NULL
#include <stdbool.h>                    // Defines true
#include <stdlib.h>                     // Defines EXIT_FAILURE
#include "definitions.h"                // SYS function prototypes
#include <string.h>
#include<stdio.h>

#define LED_On()      LED_Clear()
#define LED_Off()     LED_Set()
#define factor2         3253/4096
uint16_t txData[2];
unsigned int  v1 , v2
volatile bool transferStatus=false;
 

/* This function will be called by SPI PLIB when transfer is completed */
void SPI0_Callback(uintptr_t context )
{
    transferStatus = true;
}


 
int main ( void )
{
    
    /* Initialize all modules */
     SYS_Initialize ( NULL ); 
     SYSTICK_TimerStart();
     SPI0_CallbackRegister(SPI0_Callback, 0);
     
     /*read volatges v1 and v2*/
     AFEC1_ConversionStart ();
    while(!AFEC1_ChannelResultIsReady(AFEC_CH0)) 
    {

    
    
    };
    while(!AFEC1_ChannelResultIsReady(AFEC_CH1))
    {
        
        
    };
    
     v1 = AFEC1_ChannelResultGet(AFEC_CH0) *factor2;
     v2 = AFEC1_ChannelResultGet(AFEC_CH1) *factor2;
    
    /*assign voltages to txData*/
    txData[0] = v1;
    txData[1] = v2;
     while(1)
    {
        SYSTICK_DelayMs(1000);
        /*SPI write*/
          for(int i=0 ; i<2 ; i++)
          {
              SPI0_Write(&txData[i], sizeof(txData[i]));
              printf("v[%d] = %d\n\r",i+1, txData[i]);
          }
       
        if(transferStatus == true)
        {
            transferStatus = false;
            printf("message sent\n\r");
            LED_Clear();
            
            
           
        }

    }
}
