# Run Azure Kinect DK apps on docker 

This activity is intended to make this device available in IoT scenario. 

Please setup your Jetson Nano and Azure Kinect DK according to [../README.md](../README.md). And make sure that the K4aviewer runs propery on host environment. 

0. Update Jetson Nano Ubuntu before SDK setting  
I recommend that you'd better update your Ubuntu of Jetson Nano.  
- $ sudo apt-get update
- $ sudo apt-get dist-upgrade

1. Make directory named 'iot' in the directory where the 'Azure-Kinect-Sensor-SDK' 

- $ mkdir iot
- $ cd iot

2. Copy app which you want to run in docker and related libraries.

- $ cp ../Azure-Kinect-Sensor-SDK/build/bin/k4arecorder .
- $ cp ../Azure-Kinect-Sensor-SDK/build/bin/libk4a.so.1.4 .
- $ cp ../Azure-Kinect-Sensor-SDK/build/bin/libk4arecord.so.1.4 .

3. Create docker container by interactive mode with necessary many options and start it and create /app directory.

- $ sudo docker create --net=host --runtime=nvidia  -v /usr/lib/aarch64-linux-gnu:/usr/lib/aarch64-linux-gnu -v /lib/aarch64-linux-gnu:/lib/aarch64-linux-gnu -v /tmp/.X11-unix:/tmp/.X11-unix:rw --device=/dev/snd -v /dev/snd:/dev/snd:ro --device=/dev/bus/usb -v /dev/bus/usb:/dev/bus/usb:ro -e DISPLAY=:0 --device=/dev/video0  -v /lib/udev:/lib/udev:ro -v /lib/modules:/lib/modules:ro -v /etc/asound.conf:/etc/asound.conf:ro  --privileged --name kinect -it nvcr.io/nvidia/l4t-base:r32.3.1
- $ sudo docker start -ia kinect
- \# mkdir /app
- \# exit

4. Copy app and related libraries in docker container.

- $ sudo docker start -ia kinect
- \# cd /app
- \# ./k4arecoder --imu OFF output.mkv

Then this apps shows following messages...

Device serial number: 000205794712  
Device version: Rel; C: 1.6.102; D: 1.6.75[6109.7]; A: 1.6.14  
...   
Device started  
Started recording   
Press Ctrl-C to stop recording.

This app continues to record depth image until you press Ctrl-C.  
After do that, output.mkv file is created. 

5. You can check this app worked properly by using k4aviewer.

- \# exit
- $ sudo docker cp kinect:/app/output.mkv .
- $ cd ../Azure-Kinect-Sensor-SDK/build/bin
- $ ./k4aviewer 

6. Try you own dream apps!  
As I shown above, you can run Azure Kinect apps by only copying executable file with necessary libraries. Please try to run your apps developed by yourself. 

## Next Step
Will try to run Azure Kinect apps on [Azure IoT Edge technology](https://docs.microsoft.com/en-us/azure/iot-edge/about-iot-edge).

## Issue
Sound capture by mic array is unstable. In this container I checked that 'arecord -d 5 test.wav' work properly several times. 

But for some reason, it doesn't work. Once it doesn't work, it doesn't work no matter what you do. The only way to work correctry which I know is ... start over from setup.  
At this time I can't resolve this problem. 