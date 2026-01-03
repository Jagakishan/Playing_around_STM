This repo contains little experiments I did with Bluepill(STM32F103C8T6) and some with Blackpill. These are not part of any project, although some may be overlapping as I am working in the same workspace in CubeIDE.
Each folder demonstrates small topics like timers, GPIO, UART, DMA, I2C, CAN, DAC, etc.. 
Mostly HAL heavy codes, and some bare-metal.

Why this repo?
This is just my personal learning repo for STM32. None of the code is a full project. The learning process was prioritized, so you can;t find a completeness in most of the code. 
            **So, the repo is not for reference.**

If you still want to explore the repo, here's an ambiguous guide:

I've named experiments with topic name mostly, for easier identification. The ones with numbers like "Bluepill_2", I'll paste my notepad notes about each of them, so it'd help in someway if you got the patience.

**Bluepill_0** -> LED Blink that's it

**Bluepill_1** -> External interrupt to change the frequency of LED blinks

**Bluepill_2** -> Timer 4 used to toggle LED every 1 second with 72MHz crystal clock frequency. We handled the interrupt by overwriting the callback function.

**Bluepill_3** -> Timer 4. But instead of handling the callback, we generate the interrupt signal in channel 1 of timer 4. Which is then connected to whatever device that needs that trigger signal.
Same frequency from previous project. As a second part of this project we used another function SET_COMPARE of the timers to regulate the pulse width in the code unlike last time where we fixed it in the 
CubeMX setup.

**Bluepill_4** -> Regulated pulse width as a function of sine wave. Same as last project's part 2, but done elegantly. Two timers 3 and 4. TIM4 produces PWM signal of particular freuency in channel 1.
TIM3 triggers interrupt at a particular frequency. Each time interrupt called PWM duty cycle is changed. This duty cycle value corresponding to sine signal is calculated and stored in a look up table.
 Output frequency of PWM signal is (tim4's freq)/(tim3's frequency). This is PWM Sine wave generation using look-up table method.

**Blackpill_405xx_0** -> Sine wave generation using DMA+DAC+TIM. Timer triggers DMA to transfer values to DAC for conversion and output through DAC channel pin (PA4 I guess). 
Well there's another simpler way without the DMA, where the timer generates interrupt and the callback function is overwritten to trigger the DAC conversion. But it keeps the CPU busy. 
That's why we go for DMA which is much more elegant and efficient.

**Blackpill_405xx_1** -> :Last project we generated sine wave with a look up table. A look up table can't always be used when we have to change signal whenever we want. 
So, we used functions HAL_DAC_ConvHalfCpltCallbackCh1 and HAL_DAC_ConvHalfCpltCallbackCh1 which are called everytime DAC has been feeded half the buffer and complete buffer. 
Every clock tick DMA sends one value to DAC for conversion. Apart from that these trigger functions can be used to fill up the other half of the array, so no time is wasted.

**Bluepill_5** -> In this project we have exploited the UART. Used UART2 to transmit data and recieve it with UART3. Two ways of doing it. 
Transmit and then trigger interrupt at reciever end. This keeps the CPU busy when there is continuous data transmission and reception, triggering interrupts every byte. 
The better way is using DMA. We have used DMA only at reciever end. The DMA loads up the transmitted data in its buffer and it can be processed continuosly. 
The half full interrupt is disabled to reduce complexity. The HAL_UARTEx_RxEventCallback() function triggers interrupt as soon as the transmitted message leaves a gap for one time frame or ends. 
Apart from this everytime a message is recieved the LED is flashed for 0.1 second using TIM3.

**Bluepill_6** -> In this project, we have used I2C communication. First isDeviceReady() function is used to check if the slave sensor responds. Then we read a register from the sensor by using I2C_MASTER_TRANSMIT() and I2C_MASTER_RECIEVE(). 
Being honest, those were some lame functions. If you're about to use I2C practically, use functions HAL_I2C_MemREAD() and HAL_I2C_MemWRITE() functions. Simpler and easy to understand. Well suited to read and write values to and from registers inside sensors or other slave devices.

The ones with decimal points(Bluepill_x.y) in title might just be the extension of it's x.0 part.

## Author
Jagakishan S.K.

