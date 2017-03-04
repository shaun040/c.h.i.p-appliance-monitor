# C.H.I.P Appliance Monitor
Forked from Raspberry Pi Appliance Monitor and re-jiggered for C.H.I.P (https://www.getchip.com/)
https://github.com/Shmoopty/rpi-appliance-monitor

_Get **Tweets**, **Slack** messages, **PushBullet**, or **IFTTT triggers** notifications when appliances begin or end their cycles_

These instructions are for a simple C.H.I.P project that can make any old appliance smart, without having to operate on the appliance.  Just stick this tiny monitor onto it!

C.H.I.P Appliance Monitor makes use of the nicely sensitive 801s vibration sensor.  It will detect faint shaking and if the shaking lasts a specified amount of time, it will assume that the appliance is running. 

![On Phone](https://github.com/shaun040/shaun040.github.io/blob/master/16681841_10100253471478670_7449050954835591157_n.jpg?raw=true "On Phone")

This works on clothes washers and dryers, dishwashers, garage door openers, fans, furnaces, and other machines that vibrate.

## Needed parts:

* A C.H.I.P https://www.getchip.com/
* An [801s vibration sensor module](https://www.amazon.com/s/ref=nb_sb_noss?url=search-alias%3Dcomputers&field-keywords=801s+vibration+sensor)   You'll want one with a **voltage** (+V), **ground** (-V), and **digital signal pin**.  Mine has an extra analog sensor pin that I'm effectively ignoring.  
* Any 1 amp microUSB power source (What most phones and tablets from the last 10 years use) 

Your OS should now be ready to boot and automatically jump on your home network!

# Step 1: Create the hardware

3. Add the 801s Vibration Sensor to [C.H.I.P GPIO pins](https://docs.getchip.com/chip.html#pin-headers). I had to purchase some male to female jumper wires as the pins for the sensor don't line up VCC and GND side by side on the C.H.I.P. Ordered these on Amazon (https://www.amazon.com/gp/product/B00AC4NQYG/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1) <ADD PINS>
> Multiple sensor expert mode: Connect additional vibration modules to the same (or any) 5V and GND pins, but a different sensor GPIO pin. You'll want to use a very flexible or long cable, so one vibrating sensor doesn't vibrate everything.

4. Plug in a power source, and you’re good to go.  Within a few seconds, you should be able to connect to the C.H.I.P with: “ssh chip@*{**unique host name**}*” (password: `chip`)

# Step 2: Create the software

After you ssh to the C.H.I.P, we will create a virtualenv for the application and install a few essential libraries:

    $ sudo apt-get install python-pip 
    $ sudo pip install --upgrade virtualenv
    $ sudo adduser --system vibration
    $ sudo addgroup vibration
    $ sudo mkdir /srv/vibration
    $ sudo chown vibration:vibration /srv/vibration
    $ sudo su -s /bin/bash vibration
    $ virtualenv -p python /srv/vibration
    $ source /srv/vibration/bin/activate
    $ pip install -r requirements.txt
    
    Install CHIP GPIO Python Library
    
    $ sudo apt-get update
    $ sudo apt-get install git build-essential python-dev python-pip flex bison chip-dt-overlays -y
    $ git clone git://github.com/xtacocorex/CHIP_IO.git
    $ cd CHIP_IO
    $ sudo python setup.py install
    $ cd ..
    
Set the timezone to make sure timestamps are correct

See Language & Location settings of C.H.I.P doc (https://docs.getchip.com/chip.html#settings-and-configuration)

Create the program file [`/home/chip/vibration.py`](https://raw.githubusercontent.com/shaun040/c.h.i.p-appliance-monitor/master/vibration.py) (Click to view)

Create the settings file [`/home/chip/vibration_settings.ini`](https://raw.githubusercontent.com/sfurey/chip-appliance-monitor/master/vibration_settings.ini).  This file specifies what sensor pin to monitor, what messages you want, and what services to send the message to. 

> Multiple sensor expert mode: Create additional settings files with their own timings, messages, and unique sensor pins.  One for each sensor.

* If you want PushBullet notifications, create a PushBullet Access Token key here:  https://www.pushbullet.com/#settings/account
* If you want Twitter notifications, create Twitter API keys here (Steps 1-4): http://nodotcom.org/python-twitter-tutorial.html
* If you want Slack notifications, create a bot user: https://api.slack.com/bot-users

Edit `/etc/rc.local` to make the program run when the device boots up.

Add before the `exit` line:

    python /home/pi/vibration.py /home/pi/vibration_settings.ini &

> Multiple sensor expert mode: Add and additional line for each additional sensor. One for each settings file.

You’re done!  Reboot and test it out.

Some mounting tape or Sugru will let you stick the device somewhere discrete on your appliance.

![Completed device](https://raw.githubusercontent.com/shaun040/shaun040.github.io/master/2017-02-22.png "Completed device")
