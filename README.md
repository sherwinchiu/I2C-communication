# I2C-Communication
After learning about different communication protocols, I decided it would be fun if I took all my classmates Arduino Megas and connect them all with the I2C (I squared C, not I two C) protocol. With a little help from them, we created this masterpiece:

![image 1](https://hackster.imgix.net/uploads/attachments/1020983/20191114_135501_3nD1ylzsm1.jpg?auto=compress%2Cformat&w=740&h=555&fit=max)
**Connected Arduinos**

Although it doesn't look too impressive, I'm actually sending bytes of data to 5 different Arduino Megas. I do this with only 3 pins (DATA, CLK, and GROUND). Data can be sent 2 ways, meaning the readers can become writers with the same wires. Compared to the UART (universal asynchronous receiver-trasmitter) and the SPI (serial peripheral interface) communication protocols, this allows for transmission of data without a bunch of wires or pins being used. Each Arduino Mega gets its own data packet sent to them by my one master Mega, and if each of the Arduino's wanted to, they could become the master Mega as well.
After a while of being amazed at how awesome my boards were, I realized I couldn't even visualize the bits of data that I was sending. This is where the trusty oscilloscope comes in! I hooked up Channel 1 to Data and Channel 2 to Clock. I set 2.00V per division (per square) for both the DATA and CLK, and set the trigger on the rising edge of the clock. After fiddling around with other knobs and buttons, this is what I finally saw:

![image 2](https://hackster.imgix.net/uploads/attachments/1020993/image0_j7EWe1mOMy.jpg?auto=compress%2Cformat&w=740&h=555&fit=max)
**Non-responsive address**


Pretty neat, right? Although a little curved due to interference, it's perfectly readable. Here, we see the master attempting to communicate to an address that doesn't respond back. Both DATA and CLK pins have an active low, and have an idle high. First, the DATA pin gets pulled down, shortly followed by the CLK pin. On the rising edge of the CLKs, the data is read from the LSB (least significant bit). There are only 7 bits for the address, 1 read/write bit (LOW for write), and then 1 response bit, for a total of 9 bits. Here, we can see the address has a value of: 0000011. In decimal, 3. Since the response bit is high, no one is at the end of the line, and no one responds. I find out that my friend has wired his GND on his Mega incorrectly, but that's okay, as the bus frees itself for another response by pulling both the CLK and DATA back to high to be idle. My Mega can still talk to 4 other Megas, and one of them actually responds: (ditched that one friend)

![image 3](https://hackster.imgix.net/uploads/attachments/1020994/20191119_132757_bqw5rdu6Z2.jpg?auto=compress%2Cformat&w=740&h=555&fit=max)
**Responsive Address**

Now we're talking! Data actually being sent! It's amazing. Again, the DATA pin is pulled down, shortly followed by the CLK being pulled down. We can read the address again (which is pretty simple this time around) to being: 0000000. In decimal, I think you can guess what it is! After that, 1 read/write bit, 1 response bit (pulled low by the receiver), and then a little gap in the CLK before reading again. This time, data is actually being sent! The pattern goes 8 bits of data, followed by 1 response bit. In binary, we read: 11101000. Translating to human numbers, we read: 128 + 64 + 32 + 8 = 232! Exactly the number I was trying to send to address 0. The DATA pin goes back to high, then to low, then the CLK pin goes up, shortly followed by the data pin. My classmates and I have successively transferred data between our Arduino Megas with only 3 pins. Imagine how much data I sent to the other boards!
As I once said, that's crazy.
Seriously though, the I2C protocol is like the a combination of both the UART and SPI protocols. It allows for less pins, but is synchronous and allows for precise communication with specific devices. Its applications can vary from controlling displays, reading sensors, and controlling a network of chips with only 2 IO pins on the Arduino. Although I didn't show it control sensors/devices here, simply sending a byte through 2 wires and to 5 different boards still blows my mind. Although not the most complex project, its very cool!
