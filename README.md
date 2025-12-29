# Snapberry
Snapberry is a Raspberry pi point and shoot camera with Raspberry Pi zero  2w and Arducam 64mp Owlsight (OV64A40) which can shoot 10 bit RAW

![IMG_20251229_112515 (1)](https://github.com/user-attachments/assets/3f26c904-e155-4eba-b9bd-895ce09dede4)


The Camera consists of a Pi Zero 2W and the Arducam 64MP OwlSight OV64A40.
Due to the Pi zero 2w not having alot of memory (512mb) the camera is limited to a lower resolution ( 16mp ) , but still uses the full area 1/1.32 inch of the sensor.
this mode is refered to as "superpixel mode" by arducam becuase 4 of the pixels merge to create a bigger pixel hats how 64mp becomes 16mp ,
the good this is this leads to less noise and more dynamic range.

![IMG_20251227_120751](https://github.com/user-attachments/assets/a207ce34-9f20-4a87-a776-af71f85f39c7)

# Connections-
The connections are very simple , to power the pi im using a usb type c power delivery module so i can easily power it with any powerbank (for now).
as per the connections the shutter button is connected to GPIO 23 and focus up/down are connected with GPIO 25 and 12.
Also i have used a green led with 220 ohm resistor which is connected to GPIO 24 to show status if the camera is taking picture.
this is kind of useful becuase the pi still takes 5-6 seconds to take a picture and looking at the phone screen can be inconvinient 

![IMG_20251229_112551](https://github.com/user-attachments/assets/7905205f-de41-446f-9760-73e2489c9dcb)

 
 # IMPORTANT PART -
 the sensor apparently works only on Raspberry Pi OS 32bit lite other versions will not work with this setup.
 Follow arducams documentation to setup the camera for first use - https://docs.arducam.com/Raspberry-Pi-Camera/Native-camera/64MP-OV64A40/#1-system-configuration
 NOTE- this given command  ``` rpicam-still -t 5000 -o test.jpg ``` wont work on the pi zero 2w because the software automatically choses the highest resolution for the picture. you have to specify your desired resolution yourself for it to work 
 
 ``` rpicam-jpeg --output test.jpg --timeout 8000 --width 4624 --height 3472 --autofocus-mode continuous ``` 
 this command works best for this setup, a resolution of 4624x3472 is perfect for the pi zero 2w without an error.

 # App use -
 when camera_app.py is run it creates a website link which can be opened by any device on the local network.
 i use my phone which is connected to the pi zero 2w with the phone hotspot so they are in the same local network and typing the given link from the script opened the camera interface.
 in the website you can manually change the focus and see the preview at the top , and there is a capture button to take photos in 16mp 10 bit RAW.
 taking pictures takes upto 5-6 seconds becuase of the hardware limitations of the zero 2W.
 
 ![IMG_20251229_115206](https://github.com/user-attachments/assets/cf3c2c72-d253-4390-a8ae-aea83dd9a722)
 

 



