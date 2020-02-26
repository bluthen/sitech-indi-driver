# sitech-indi-driver
INDI driver for Sideral Technology's Servo controllers


Install Steps:
```
sudo apt install git build-essential cmake libindi-dev libnova-dev libgsl-dev libusb-1.0-0-dev \
libcfitsio-dev libdc1394-22-dev libgps-dev libftdi1-dev libraw-dev

git clone https://github.com/indilib/indi-3rdparty.git
cd indi-3rdparty
git clone https://github.com/bluthen/sitech-indi-driver.git indi-sitech
cd indi-sitech
mkdir build
cd build
cmake ..
make
sudo cp indi_sitech_telescope /usr/bin
sudo cp indi_sitech.xml /usr/share/indi
```
