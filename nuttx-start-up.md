# Nuttx start up

### 1. Hardware

The [STM32F429 Discovery](http://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-eval-tools/stm32-mcu-eval-tools/stm32-mcu-discovery-kits/32f429idiscovery.html) kit \(32F429IDISCOVERY\) allows users to easily develop applications with the STM32F429 high-performance MCUs with **ARM®Cortex®-M4** core.

It includes an ST-LINK\/V2 or ST-LINK\/V2-B embedded debug tool, a 2.4" QVGA TFT LCD, an external 64-Mbit SDRAM, an ST MEMS gyroscope, a USB OTG micro-AB connector, LEDs and push-buttons.

**Key Features**

* STM32F429ZIT6 microcontroller featuring 2 Mbytes of Flash memory, 256 Kbytes of RAM in an LQFP144 package_ On-board ST-LINK\/V2 on STM32F429I-DISCO or ST-LINK\/V2-B on STM32F429I-DISC1_ mbed™ -enabled \(mbed.org\) with ST-LINK\/V2-B only\* USB functions:

  * debug port _ virtual COM port with ST-LINK\/V2-B only _ mass storage with ST-LINK\/2-B only

* Board power supply: through the USB bus or from an external 3 V or 5 V supply voltage

* 2.4" QVGA TFT LCD

* 64-Mbit SDRAM

* L3GD20, ST MEMS motion sensor 3-axis digital output gyroscope

* Six LEDs:

  * LD1 \(red\/green\) for USB communication _ LD2 \(red\) for 3.3 V power-on _ Two user LEDs: LD3 \(green\), LD4 \(red\) \* Two USB OTG LEDs: LD5 \(green\) VBUS and LD6 \(red\) OC \(over-current\)

* Two push-buttons \(user and reset\)

* USB OTG with micro-AB connector

* Extension header for LQFP144 I\/Os for a quick connection to the prototyping board and an easy probing

* Comprehensive free software including a variety of examples, part of STM32CubeF4 package or STSW-STM32138 for legacy standard libraries usage


### 2. Start up

**Cortex-M4** core is a high performance embedded processor with a full-featured **ARMv7-M** instruction set. in nuttx source tree, we can find **ARMv7-M' **s port\(nuttx\/arch\/arm\/armv7-m\).

1. STM32F429 start from vector table\(reset exception handler\) on reset, this part code can find in arch\/arm\/armv7-m\/\_\_**_up-vectors.c_**

  ```c
  unsigned _vectors[] __attribute__((section(".vertors"))) = {
  IDLE_STACK,             /* Initial stack */
  (unsigned)&__start,     /* Reset exception handler */
  [2 ... (15 + ARMV7M_PERIPHERAL_INTERRUPTS)] = (unsigned)&exception_common  /* all others point to genertic handler */
  } 
  ```

  * init stack pointer\(sp\)
  * set up reset exception handler
  * set up all other exceptions to generic handler, for safety

2. nuttx\/arch\/arm\/src\/stm32\/armv7-m\/stm32\_start.c

  ```c
  /* set up clock, init FPU and serial port */
  stm32_clockconfig();        // set up clock, HSE
  stm32_fpuconfig();          // FPU init
  stm32_lowsetup();           // set up serial port for console output
  stm32_gpioinit();           // remap gpio's alternative functions, accoring to .confi

  /* Clear .bss section */
  for(dest=_START_BSS; dest < _END_BSS)
  {
      *dest++ = 0;
  }
  g
  ```


