# OctoPrint For MegaPi with Raspberry Pi
![image](https://github.com/xeecos/OctoPrintForRpi/raw/master/images/4.jpg)
 * Checkout OctoPrint: git clone https://github.com/foosel/OctoPrint.git
 * Change into the OctoPrint folder:cd OctoPrint
 * Install OctoPrint: python setup.py install
 * Start the server: octoprint
  ![image](https://github.com/xeecos/OctoPrintForRpi/raw/master/images/2.jpg)
 
## Prepare For MegaPi
 * Use this to define additional glob patterns matching serial ports to list for connecting against, e.g. /dev/ttyAMA*. One entry per line.
 ![image](https://github.com/xeecos/OctoPrintForRpi/raw/master/images/1.jpg)

## Webcam

If you also want webcam and timelapse support, you'll need to download and compile MJPG-Streamer:
```
cd ~
sudo apt-get install subversion libjpeg8-dev imagemagick libav-tools cmake
git clone https://github.com/jacksonliam/mjpg-streamer.git
cd mjpg-streamer/mjpg-streamer-experimental
export LD_LIBRARY_PATH=.
make
```
This should hopefully run through without any compilation errors. You should then be able to start the webcam server using:
```
./mjpg_streamer -i "./input_uvc.so" -o "./output_http.so"
```
If you want to use the official RaspberryPi Camera Module you need to run
```
./mjpg_streamer -i "./input_raspicam.so -fps 5" -o "./output_http.so" 
```
## Make everything accessible on port 80
Setup on Raspbian is as follows:
```
sudo apt-get install haproxy
```
Edit /etc/haproxy/haproxy.cfg, for further examples take a look [here](https://github.com/foosel/OctoPrint/wiki/Reverse-proxy-configuration-examples#haproxy):
```
global
        maxconn 4096
        user haproxy
        group haproxy
        daemon
        log 127.0.0.1 local0 debug

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        retries 3
        option redispatch
        option http-server-close
        option forwardfor
        maxconn 2000
        timeout connect 5s
        timeout client  15min
        timeout server  15min

frontend public
        bind *:80
        use_backend webcam if { path_beg /webcam/ }
        default_backend octoprint

backend octoprint
        reqrep ^([^\ :]*)\ /(.*)     \1\ /\2
        option forwardfor
        server octoprint1 127.0.0.1:5000

backend webcam
        reqrep ^([^\ :]*)\ /webcam/(.*)     \1\ /\2
        server webcam1  127.0.0.1:8080
```
This will make OctoPrint accessible under http://<your Raspi's IP>/ and make mjpg-streamer accessible under http://<your Raspi's IP>/webcam/.

You'll also need to modify /etc/default/haproxy and enable HAProxy by setting ENABLED to 1.

After that you can start HAProxy by issuing the following command

'''
sudo service haproxy start
'''
Pointing your browser to http://<your Raspi's IP> should greet you with OctoPrint's UI. 

Now open the settings and switch to the webcam tab. 

Set the webcam's stream URL from http://<your Raspi's IP>:8080/?action=stream to /webcam/?action=stream and reload the page.

![image](https://github.com/xeecos/OctoPrintForRpi/raw/master/images/5.jpg)

![image](https://github.com/xeecos/OctoPrintForRpi/raw/master/images/3.jpg)
