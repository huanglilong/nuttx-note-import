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

1. STM32F429 start from vector table\(reset exception handler\) on reset, this part code can find in arch\/arm\/armv7-m\/\_\_up-vectors.c

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
  /* Set up clock, init FPU and serial port */
  stm32_clockconfig();        // set up clock, HSE
  stm32_fpuconfig();          // FPU init
  stm32_lowsetup();           // set up serial port for console output
  stm32_gpioinit();           // remap gpio's alternative functions, accoring to .confi

  /* Clear .bss section */
  for(dest=_START_BSS; dest < _END_BSS)
  {
  *dest++ = 0;
  }

  /* Copy initialized data section(global initialized variable) from FLASH to SRAM */
  for(src = _DATA_INIT, dest = _START_DATA; dest < _END_DATA)
  {
  *dest++ = *src++;
  }

  /* Initialize board devices, such as SPI, LED, USB... */
  stm32_boardinitialize(); // init board devices

  /* call os_start() function, initialize and start os */
  os_start();
  ```


### 3. Nuttx init and start up

1. nuttx\/sched\/init\/os\_start.c

  ```
  /* Initialize RTOS Data */
  dp_init(&g_readytorun);
  dq_init(&g_pendingtasks);
  dq_init(&g_waitingforsemaphore);
  dq_init(&g_waitingforsignal);
  dq_init(&g_waitingformqnotfull);
  dq_init(&g_waitingformqnotempty);

  /* Initialize process IDS */
  g_lastpid = 0;
  for(i=0; i < CONFIG_MAX_TASKS; i++)
  {
  g_pidhash[i].tcb = NULL;
  g_pidhash[i].pid = INVALID_PROCESS_ID;
  }

  /* Initialize the IDLE task TCB */
  hashndx = PIDHASH(g_lastpid); // force task's ID in the valid range
  g_pidhash[hashndx].tcb = &g_idletcb[cpu].cmn; // TCB's common part
  g_pidhash[hashndx].pid = g_lastpid;           // =0
  bzero((void *)&g_idletcb[cpu], sizeof(struct task_tcb_s));  // clear all fileds
  g_idletcb[cpu].cmn.pid = g_lastpid;                         // idle task's PID
  g_idletcb[cpu].cmn.task_state = TSTATE_TASK_RUNNING;        // task state

  /* Set IDLE entry point */
  g_idletcb[cpu].cmn.start = (start_t)os_start;
  g_idletcb[cpu].cmn.entry.main = (main_t)os_start;

  /* IDLE task is kernel thread */
  g_idletcb[cpu].cmn.flags = TCB_FLAG_TTYPE_KERNEL;

  /* IDLE task's name and args */
  g_idleargv[cpu][1] = NULL;                 // no args
  g_idletcb[cpu].argv = &g_idleargv[cpu][0]; // task's name + args

  /* Add IDLE task's TCB to the head of ready to run list */
  tasklist = TLIST_HEAD(TSTATE_TASK_RUNNING);               // get g_readytorun list
  dq_addfirst((FAR dq_entry_t *)&g_idletcb[cpu], tasklist); // add idle task's TCB into g_readytorun list
  up_initial_state(&g_idletcb[cpu].cmn);                    // init TCB's processor-specific registers part

  /* Initialize RTOS facilities */
  sem_initialize();
  task_initialize();
  g
  ```


