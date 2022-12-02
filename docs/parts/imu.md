# IMU

IMUs or inertial measurement units are parts that sense the inertial forces on a robot. They vary depending on sensor, but may commonly include linear and rotational accelleration. They may sometimes include magnetometer to give global compasss facing dir. Frequently temperature is available from these as it affects their sensitivity.

## MPU6050/MPU9250

This is a cheap, small, and moderately precise imu. Commonly available at [Amazon](https://www.amazon.com/s/ref=nb_sb_noss_2?url=search-alias%3Dindustrial&field-keywords=MPU6050).

MPU9250 offers additional integrated magnetometer.

* Typically uses the I2C interface and can be chained off the default PWM PCA9685 board. This configuration will also provide power.
* MPU6050: Outputs acceleration X, Y, Z, Gyroscope X, Y, Z, and temperature.
* MPU9250: Outputs acceleration X, Y, Z, Gyroscope X, Y, Z, Magnetometer X, Y, Z and temperature.
* Chip built-in 16bit AD converter, 16bit data output
* Gyroscopes range: +/- 250 500 1000 2000 degree/sec
* Acceleration range: ±2 ±4 ±8 ±16g

### Software Setup

Install smbus2

* either using pip:

``` bash
 pip install smbus2
```
* or conda:

``` bash
 conda install -c conda-forge smbus2
```


**If you are using MPU6050:** 

Install pip lib for `mpu6050`:

```bash
pip install mpu6050-raspberrypi
```

**If you are using MPU9250:** 

Install pip lib for `mpu9250-jmdev`:

```bash
pip install mpu9250-jmdev
```

### Configuration
1. Make the following configuration changes in your `/home/pi/mycar/myconfig.py`:

``` python
#IMU
HAVE_IMU = True
IMU_SENSOR = 'mpu9250'          # (mpu6050|mpu9250)
IMU_DLP_CONFIG = 0x05           # Digital Lowpass Filter setting (0x00:250Hz, 0x01:184Hz, 0x02:92Hz, 0x03:41Hz, 0x04:20Hz, 0x05:10Hz, 0x06:5Hz)
```
`IMU_SENSOR` can be either `mpu6050` or `mpu9250` based on the sensor you are using.

`IMU_DLP_CONFIG` allows to change the digital lowpass filter settings for your IMU. Lower frequency settings (see below) can filter high frequency noise at the expense of increased latency in IMU sensor data.
Valid settings are from 0 (`0x00`) to 6 (`0x06`):

- `0` 250Hz
- `1` 184Hz
- `2` 92Hz
- `3` 41Hz
- `4` 20Hz 
- `5` 10Hz
- `6` 5Hz

<br>

2. In `/home/pi/projects/donkeycar/donkeycar/parts/` find `imu.py`

On lines `29`,`45` and `54` of `imu.py`, implement the following adjustments.

* On line `29` change `addr=0x68` to `addr=0x69` (`...def __init__(self, addr=0x69, poll_delay=0.0166, ...`)

* On line `45` change `address_mpu_slave=None` to `address_mpu_slave=0x0c`

* On line `54` change `self.sensor.calibrateMPU6500()` to `#self.sensor.calibrateMPU6500()` (add `#` at the start of the line)

<br>

### Notes on MPU9250
If you are using Robohat MM1, the MPU9250 come presoldered and the following is applicable to you.
The self-calibration routine currently does not function.
This is why we comment out the corresponding line `54` in the second configuration step above.
Factory calibration is thus considered as minimum sufficient.

In case the calibration routine gets fixed in the future, the following applies:
At startup the MPU9250 driver performs calibration to zero accel and gyro bias. 
Usually this process takes less than 10 seconds.
Avoid moving or touching the car during this time.
It is best to place the car on flat ground when first launching a driving script that relies on the imu.
