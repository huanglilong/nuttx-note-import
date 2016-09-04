# NSH start up

### 1.nsh init and start up

1. apps/examples/nsh/nsh_main.c
  ```c
  up_cxxinitialize(); // initialization of the static C++ class instances

  /* Initialize the NSH library */
  nsh_initialize();
  {
      /* Mount the /etc filesystem */
      (void)nsh_romfsetc();
  }
  nsh_consolemain(0, NULL);
  ```


