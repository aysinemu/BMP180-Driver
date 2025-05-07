# BMP180-Driver

### Hardware requirements

- [Raspberry Pi 3 Model B+](https://raspberrypi.vn/san-pham/raspberry-pi-3-model-b)
- [BMP180](https://hshop.vn/cam-bien-ap-suat-khong-khi-bmp180)

### Connect

 +-----+-----+---------+------+---+---Pi 3B+-+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 |     |     |    3.3v |      |   |  1 ||    |   |      | 5v      |     |     |
 |   2 |   8 |   SDA.1 | ALT0 | 1 |  3 ||    |   |      | 5v      |     |     |
 |   3 |   9 |   SCL.1 | ALT0 | 1 |  5 || 6  |   |      | 0v      |     |     |
  +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+

### Clone project
```
git clone https://github.com/aysinemu/BMP180-Driver.git

cd BMP180-Driver
```

### Building and Installing the Module

```
Step 0 : Add BMP180 to your Device Tree.

cd /boot

ls #Find your Ras

sudo dtc -I dtb -O dts -o Your_Ras.dts Your_Ras.dtb

sudo nano Your_Ras.dts

Find i2c@7e804000 in Your_Ras.dts and replace that with this

i2c@7e804000 {
        compatible = "brcm,bcm2835-i2c";
        reg = <0x7e804000 0x1000>;
        interrupts = <0x02 0x15>;
        clocks = <0x08 0x14>;
        #address-cells = <0x01>;
        #size-cells = <0x00>;
        status = "disabled";
        pinctrl-names = "default";
        pinctrl-0 = <0x15>;
        clock-frequency = <0x186a0>;
        phandle = <0x2b>;
        bmp180@77 {
                    compatible = "bosch,bmp180";
                    reg = <0x77>;
            };
};

Ctrl+0 and Enter

Ctrl+X

sudo dtc -I dts -O dtb -o Your_Ras.dtb Your_Ras.dts

sudo reboot

Step 1 : Check if it has the BMP module; if so, remove it.

lsmod | grep bmp

sudo rmmod module_name

Step 2 : Check if you have connected your module to the Raspberry Pi 3+, and if it reacts correctly.

sudo i2cdetect -y 1

Show : 
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- 77           

Step 3 : Install driver.

Make

sudo insmod bmp180_driver.ko
```

### Uninstalling the Module

```
sudo rmmod bmp180_driver
```



### Run User Interface Code

```
gcc -o test_bmp test_bmp.c

sudo ./test_bmp
```

### Example of testing

```
pi@raspberrypi:~/Desktop/driver $ sudo ./test_bmp 
Temperature (raw): 25121
Pressure (raw): 41956
```

### Tips

Show the kernel log to check the install state or instantiated:
```
dmesg | tail 
```

To scan I2C bus 1 for connected I2C devices:
```
i2cdetect -y 1
```

To list all module installed:

```
ls /dev
```
