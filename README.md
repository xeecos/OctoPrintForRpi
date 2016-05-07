# OctoPrint For MegaPi with Raspberry Pi
![image](https://github.com/xeecos/OctoPrintForRpi/raw/master/images/5.jpg)
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
