# How to add driver to Nuttx

### 1. add configure variables for MPU6050

MPU6050 uses I2C interface to commniucate to with MCU, so we need define two variables for MPU6050 in the configure process.

CONFIG\_I2C: using for MPU6050 I2C interface

CONFIG\_MPU6050: using for compile 

### 2.add borad support

nuttx-configs\/stm32f429discovery\/src\/stm32\_mpu6050.c

```c
/************************************************************************************
 * Name: stm32_mpu6050_initialize
 * Description:
 * Initialize and register the MPU6050 Pressure Sensor driver.
 ************************************************************************************/
int stm32_mpu6050_initialize(FAR const char *devpath)
{
 FAR struct i2c_master_s *i2c;
 int ret;
 i2c = stm32_i2cbus_initialize(MPU6050_I2C_PORTNO); /* Initialize I2C */
 if (!i2c)
 {
  return -ENODEV;
 }
 ret = mpu605_register(devpath, i2c); /* register the mpu6050 sensor */
 if(ret < 0)
 {
  snerr("ERROR: Error registering MPU6050\n");
 }  
 return ret;
}
```



 



