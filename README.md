# raspberry-pi-ai-camera
A complete guide to setting up and using the Raspberry Pi AI Camera. Covers installation, using built-in models and Python scripts, and running your own custom models. Great for getting started or expanding your AI projects on the Pi.

Preper Raspberry Pi media with OS and settings:

Use Raspberry Pi Imager to preper your storage for the Pi
- Choose Device: Raspberry Pi 5
- Choose OS: Raspberry Pi OS (64-bit)
- Storage: Select the storage that you going to use for the Pi

click NEXT

You will be asked for OS custimisation settings
- Edit Settings

under General you can set:
- hostname
- username and password
- configure wireless LAN
- set locale settings (Time zone and keyboard layout)

under Services you can set:
- Enable SSH

click SAVE   
click YES   
You will be warn that all the data on the selected storage media going to be deleted and asked if you want to continue.   
click YES   
by the end of this process you going to have a bootable media for your raspberry pi, remove the media and insert it to the raspberry pi and power up the pi.   


update Raspberry Pi and install all needed packages:   
Open terminal on the Raspberry Pi and run the next commands:
- sudo apt update && sudo apt full-upgrade
- sudo apt install imx500-all
- sudo reboot

After the Pi rebooted, Open terminal and run the next commands:
- sudo apt install python3-opencv python3-munkres
- mkdir -p projects/raspi_ai_cam/test1/models
- cd projects/raspi_ai_cam
- git clone https://github.com/raspberrypi/picamera2.git
- git clone https://github.com/raspberrypi/imx500-models.git
- cd imx500-models
- git checkout ec3b84cd7561c743de2f3073cd4c71348e6b0e3f
- cd ..
- cp -a picamera2/examples/imx500/. test1/
- cp /usr/share/imx500-models/* test1/models/
- cp imx500-models/imx500_network_yolov8n_pp.rpk test1/models/


At this point we are ready to run the AI detection model:
- cd test1
- python imx500_object_detection_demo.py --model models/imx500_network_yolov8n_pp.rpk

To trigger event based on detection of a object, let's add few lines of code to imx500_object_detection_demo.py
- start edit the file imx500_object_detection_demo.py
- go all the way to the end of the file, one line above the line "while True:" add labels = get_labels()
- after the last line of code inside the while loop add:

if (len(last_results) > 0):   
&nbsp; &nbsp; for result in last_results:   
&nbsp; &nbsp; &nbsp; &nbsp; label = f"{labels[int(result.category)]} ({result.conf:.2f})"   
&nbsp; &nbsp; &nbsp; &nbsp; if (labels[int(result.category)] == "person"):   
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; print(f"Detected {label}")   
