# Task-6
#include <ch32v00x.h>
#include <debug.h>
#include<stdio.h>

#define LED1_PIN GPIO_Pin_4 // Yellow LED
#define LED2_PIN GPIO_Pin_5 // Red LED
#define LED3_PIN GPIO_Pin_6 // Green LED
#define LED_PORT GPIOD

void GPIO_Config(void) {
    // Enable the clock for GPIOD
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);

    // Configure PD4, PD5, and PD6 as outputs
    GPIO_InitTypeDef GPIO_InitStructure;
    GPIO_InitStructure.GPIO_Pin = LED1_PIN | LED2_PIN | LED3_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // Push-pull output
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(LED_PORT, &GPIO_InitStructure);

    // Ensure all LEDs start OFF
    GPIO_ResetBits(LED_PORT, LED1_PIN | LED2_PIN | LED3_PIN);
}

void compare_2bit(uint8_t a, uint8_t b) {
    // Clear all LEDs
    GPIO_ResetBits(LED_PORT, LED1_PIN | LED2_PIN | LED3_PIN);

    if (a > b) {
        GPIO_SetBits(LED_PORT, LED1_PIN); // a > b → Yellow LED ON
    } else if (a == b) {
        GPIO_SetBits(LED_PORT, LED2_PIN); // a == b → Red LED ON
    } else {
        GPIO_SetBits(LED_PORT, LED3_PIN); // a < b → Green LED ON
    }  
}

int main(void) {   
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1); // Fix: Changed from NVIC_PriorityGroup_2
    SystemCoreClockUpdate();
    Delay_Init();

    // Initialize the GPIO for the LEDs
    GPIO_Config();

    // Main loop to iterate over all possible 2-bit numbers  
    for (uint8_t a = 0; a <= 3; a++) {
        for (uint8_t b = 0; b <= 3; b++) {
            compare_2bit(a, b);
            Delay_Ms(2000); // Reduced delay to 2s for better visualization

            // Optional: Briefly turn off LEDs between changes to improve clarity
            GPIO_ResetBits(LED_PORT, LED1_PIN | LED2_PIN | LED3_PIN);
            Delay_Ms(200); // Short blink before next transition
        }
    }
    
    return 0; // Fix: Manually retype this line to remove non-printable characters
}
