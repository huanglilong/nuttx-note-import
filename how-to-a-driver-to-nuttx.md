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

### 3.define mpu6050 device's private data

```c
struct mpu6050_dev_s
{
 FAR struct i2c_master_s *i2c;
 uint8_t addr; /* MPU6050 I2C address */
 int freq; /* MPU6050 Frequency <= 4KHz */
 float acc_x; /* accelerometer x axis */
 float acc_y; /* accelerometer y axis */
 float acc_z; /* accelerometer z axis */
 float gy_x; /* gyroscope x axis */
 float gy_y; /* gyroscope y axis */
 float gy_z; /* gyroscope z axis */
};
```

### 4.mpu6050 private function

```c
/**************************************************************************** 
* Private Function Prototypes 
****************************************************************************/
static uint8_t mpu6050_getreg8 (FAR struct mpu6050_dev_s *priv, uint8_t regaddr);
static uint16_t mpu6050_getreg16(FAR struct mpu6050_dev_s *priv, uint8_t regaddr);
static void mpu6050_putreg8 (FAR struct mpu6050_dev_s *priv, uint8_t regaddr, uint8_t regval);
static void mpu6050_set_clock_source(FAR struct mpu6050_dev_s *priv, uint8_t source);
static void mpu6050_set_full_scale_gyro_range(FAR struct mpu6050_dev_s *priv, uint8_t range);
static void mpu6050_set_full_scale_accel_range(FAR struct mpu6050_dev_s *priv, uint8_t range);
static void mpu6050_set_sleep_enabled(FAR struct mpu6050_dev_s *priv, uint8_t enabled);
static uint8_t mpu6050_get_device_id(FAR struct mpu6050_dev_s *priv);
static void mpu6050_set_i2c_master_mode_enabled(FAR struct mpu6050_dev_s *priv, uint8_t enabled);
static void mpu6050_set_i2c_bypass_enabled(FAR struct mpu6050_dev_s *priv, uint8_t enabled);
static void mpu6050_initialize(FAR struct mpu6050_dev_s *priv);
static uint8_t mpu6050_is_rdy(void);
static void mpu6050_get_motion(FAR struct mpu6050_dev_s *priv);
static void mpu6050_init_gyro_Offset(void);
static void mpu6050_interrupt_config(FAR struct mpu6050_dev_s *priv);
static void mpu6050_write_bits(FAR struct mpu6050_dev_s *priv, uint8_t regaddr, uint8_t bit_start, uint8_t len, uint8_t data);
static void mpu6050_write_bit(FAR struct mpu6050_dev_s *priv, uint8_t regaddr, uint8_t bit_num, uint8_t data);

```

### 5.character driver methods for file system

```c
/* Character driver methods */
static int mpu6050_open (FAR struct file *filep);
static int mpu6050_close(FAR struct file *filep);
static ssize_t mpu6050_read (FAR struct file *filep, FAR char *buffer, size_t buflen);
static ssize_t mpu6050_write(FAR struct file *filep, FAR const char *buffer, size_t buflen);

/**************************************************************************** 
* Private Data 
****************************************************************************/
static const struct file_operations g_mpu6050fops ={ 
mpu6050_open, /* open */ 
mpu6050_close, /* close */ 
mpu6050_read, /* read */ 
mpu6050_write, /* write */
 0, /* seek */
 0, /* ioctl */
#ifndef CONFIG_DISABLE_POLL 0, /* poll */
#endif 
0 /* unlink */
};
```

