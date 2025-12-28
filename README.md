# Picam
A Raspberry pi point and shoot camera with Raspberry Pi zero  2w and Arducam 64mp Owlsight (OV64A40)

![IMG-20251227-WA0000](https://github.com/user-attachments/assets/7477bae3-037d-4a5f-a002-1932d51ca378)

The Camera consists of a Pi Zero 2W and the Arducam 64MP OwlSight OV64A40.
Due to the Pi zero 2w not having alot of memory (512mb) the camera is limited to a lower resolution ( 16mp ) , but still uses the full area 1/1.32 inch of the sensor.
this mode is refered to as "superpixel mode" by arducam becuase 4 of the pixels merge to create a bigger pixel hats how 64mp becomes 16mp ,
the good this is this leads to less noise and more dynamic range.

![IMG_20251227_120751](https://github.com/user-attachments/assets/a207ce34-9f20-4a87-a776-af71f85f39c7)


 # IMPORTANT PART -
 the sensor apparently works only on Raspberry Pi OS 32bit lite other versions will not work with this setup.
 Follow arducam`s documentation to setup the camera for first use - https://docs.arducam.com/Raspberry-Pi-Camera/Native-camera/64MP-OV64A40/#1-system-configuration
 NOTE- this given command ( rpicam-still -t 5000 -o test.jpg ) wont work on the pi zero 2w because the software automatically choses the highest resolution for the picture. you have to specify your desired resolution yourself for it to work
 ( rpicam-jpeg --output test.jpg --timeout 8000 --width 4624 --height 3472 --autofocus-mode continuous )
 this command works best for this setup, a resolution of 4624x3472 is perfect for the pi zero 2w without an error.

 # App use -
 when camera_app.py is run it creates a website link which can be opened by any device on the local network.
 in the website you can manually change the focus and see the preview at the top , and there is a capture button to take photos in 16mp 10 bit RAW.
 taking pictures takes upto 5-6 seconds becuase of the hardware limitations of the zero 2W.
 



