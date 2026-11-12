# Debugging

## Verifying Connection Between STM32 and MTCH2120
Source File: Module_Test.c
Test Case: MTCH2_DRIVER_TEST
There were 2 main ways that we fixed the verification between the MTCH2120 Touch Controller and the STM32.
1. Added a delay before MTCH2120 initialization because the STM32 starts executing code faster than the MTCH2120 finishes its power-on initialization.
   According to the MTCH2120 datasheet, a typical initialization time is approximately 250 ms. Therefore, the HAL_Delay(300U) before MTCH2120 initialization
   gives the MTCH2120 time to finish starting. Without the delay, the STM32 kept sending the address while the MTCH2120 was still starting. Therefore, there
   was no acknowledgement of the touch controller which led to the DEVICE_NOT_FOUND error.
2. Initially, with this test, we only had VDD, SCL, SDA, and GND connections between the STM32 and the MTCH2120. However, the MTCH2120 RESET pin is active-low.
   When testing continuity between the STM32 and the MTCH2120 external host pins, the RESET pin measured close to 0V. When the RESET pin on the MTCH2120 = 0V, the
   touch controller was held in reset. Therefore, we added an external 10k ohm pull-up resistor in series between the 3.3V line of the STM32 and the RESET pin on the MTCH2120.
   This allowed for RESET to equal 3.3V, which allowed the controller to run.
