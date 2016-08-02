# Nuttx configure and build

### 1. configure nuttx

first you should download nuttx source code

* $ git clone https://bitbucket.org/nuttx/apps* $ git clone https://bitbucket.org/nuttx/nuttx* $ cd nuttx/tools* $ ./configure.sh stm32f429i-disco/nsh

### 2. build nuttx

* $ cd nuttx* $ make ![](/assets/nuttx build.png)

### 3. flash nuttx binary

In Ubuntu, you need [st-flash tool](https://github.com/texane/stlink)

In Windows, download [st-link utility](http://www.st.com/content/st_com/en/products/embedded-software/development-tool-software/stsw-link004.html)

![](/assets/nuttx flash.png)
