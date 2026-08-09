8/7 journal prewrite


This session is the first session for wiring the schematic.
I first spent some time researching the exact mcu and uwb chip I'm going to use for the anchors.
In my pitch, i already said that i am going to use the nrf 52840 and a dw3000 family chip. However, I looked for alternatives from the nrf 52840 because I didn’t want to deal with zephyr. I looked around the internet and found out that the nrf52840 have rust support and can deal with math really well.
The second thing is the package. I used the nrf 5340 qfaa before but it was a nightmare since it is impossible to use the inner ring of the chip without vippo. I did some research to pick the package for the nrf. I found out that the qfn package (48 pins) doesn’t support usb and have the high voltage pin shorted to the regular vdd pin, so using a dc dc thingy is impossible. This doesn’t really hurt the functionality but it does hurt power efficiency. But since the nrf is known for its power efficiency, it doesn’t really matter all that much (at least compared to an esp 32).
<img width="984" height="480" alt="Screenshot 2026-08-08 020003" src="https://github.com/user-attachments/assets/301531d0-e59c-4cf0-86c4-f09cc116c231" />
This research is around 30 minutes since i also skimmed through the datasheets of each alternate chip and compare the pros and cons. Said chip includes stuff like the esp32 c family and lower power stm 32.
Power delivery is very important in sensitive rf applications like this, so i took some extra care to find the best charging ic i can find. I found a really cool ic that just outputs a clean and regulated power.
<img width="984" height="480" alt="Screenshot 2026-08-08 020003" src="https://github.com/user-attachments/assets/301531d0-e59c-4cf0-86c4-f09cc116c231" />

It is the ic to the right. The BQ24075RGT is a cool power delivery manager that takes in usb power and charges the battery, and then does some cool internal stuff that outputs power. I skimmed through the datasheet and then finally completed the charging ic. It took around 1 hour to do these.
Now i needed to wire up the dw3210 and the nrf 52840 qfaa r7. It took a long time to get the footprints because all the ones i found are buns. 

<img width="1497" height="674" alt="Screenshot 2026-08-08 021752" src="https://github.com/user-attachments/assets/419f1068-8eb0-45d3-b97d-1df99ca5a4c9" />

You can tell how the symbol is really bad. The pins with multiple functions (like analog input, gpio, qspi) and stuff like that aren’t properly labled. They just put the number on there.
I tried to look for other symbols from other sites but they dont have this specific package available. In the end i have to make my own. I will probably change it soon so that when i wire it it will look good.
<img width="605" height="763" alt="Screenshot 2026-08-08 022008" src="https://github.com/user-attachments/assets/2f704bb4-9adc-4932-9d6f-cb5aedb0c7bb" />

This is what i had by the end of remaking everything. This, along with finding all the junk, took around 45 minutes.

The dw3210 symbol took a while aswell. It turns out that only qorvo (afaik) have the symbols and i have been looking at the wrong sources the entire time. It looked something like this

<img width="377" height="466" alt="Screenshot 2026-08-08 022304" src="https://github.com/user-attachments/assets/1085ea85-af7f-4f8b-8e28-78f35a1db35a" />

I guess this one is a little better but everything is marked as passive so i have to change that soon. It appears that this is meant to replicate the actual footprint, so i guess its fine. Still lazy for not making this optimized for real world usage tho (like vss on the top).
<img width="1202" height="870" alt="Screenshot 2026-08-08 015954" src="https://github.com/user-attachments/assets/41d83d23-9e0a-458e-8559-1b24c9dbceec" />


This is what i had in the end. I spent around 45 minutes messing around with the power. I am worried that i will mess something up so i double and triple checked. I ended up using the reference design as of right now. But i will change it if i need to cuz of space constraints and whatnot. 
I took 45 minutes to look at the datasheet to know whats going on with the symbols you see on the left. The top left is just spi stuff but exton wake up and rstn was worth looking into. Exton i dont really know what to do yet since it lowk just pulls high when its doing stuff and pulls low when its not doing stuff. Ig i can link it to some gpio but its prolly useless.
Wake up is just waking up. Rstn is just reset, though i thought i needed pull up resistors but i realized since the nrf will be powered on in conjunction with the dw3210 anyway i might aswell just let the mcu take full control.
The middle left is just gpio stuff. For the netlabels not named as the gpio, i will use it as led indicators. You can see what it does by looking at the name. However, the gpios are hella butt because of this
0.8 mA does absolutely nothing. I guess i will use a cheap mosfet to drive the led ig, but thats if i have the space. Reading the dc characteristics of this took like 15 minutes to do.
Total time spent - 4 hours

8/8 prelim journal

I spent 30 minutes on fixing my schematic with my friend who knows how to route. <img width="1187" height="796" alt="Screenshot 2026-08-08 033142" src="https://github.com/user-attachments/assets/e3097999-7415-4dac-9731-b4eecb491232" />

I left sbu1 and 2 floating, added correct capacitor values to the charging ic, added correct capacitor values to the dw3210, fixed the rf configuration since 2pf cap not required for r2, fixed led direction, and a bunch of other stuff.

8/9 prelim journal

I finished the schematic for the anchor. 
<img width="955" height="861" alt="Screenshot 2026-08-09 015838" src="https://github.com/user-attachments/assets/d85ba5ed-4ea7-47b4-81d8-aa2449bc8e82" />
The first thing I did was to optimize what pins i am going to use. I specifically made it so that the pins are divided into different functions and also the what side they are on in the physical footprint. I made the spi lines so that they wont have to do as much curves nor travel as long compared to if i just randomly picked it. 
On the left hand side of the schematic, I finished wiring all the decoupling capacitors, filters for the antenna and added LF filtering for the dc dc converter for better power efficiency. I confused reg0 and reg1, which is just the stages of power delivery in the nrf 52840, which made me think that dc dc is not available in this package. But it was, it was actually the LDO that was unavailable. 
I got a friend to double check my work and fixed my mistakes, i missed a capacitor for the antenna filtering and changed some values.

<img width="385" height="598" alt="Screenshot 2026-08-09 020235" src="https://github.com/user-attachments/assets/1882dca4-7761-4941-85e1-2524d4ec190f" />

I made some connectors so that i can add additional sensors very easily when i build it in real life. Like a temperature or humidity sensor. There is no embedded sensors in this schematic. 
I will add gnd and 3v3 rails later.
<img width="479" height="539" alt="Screenshot 2026-08-09 020445" src="https://github.com/user-attachments/assets/c952e071-62d3-480b-851c-2dfc023da50c" />
I referenced the datasheet for the maximum values for ppm tolerance , ESR, load capacitance, and all the other specs. Then, i input the values that are valid in digikey and then i settled with these two crystals. One 32mhz one and the other 32.768 khz. They are pretty good, and since lower load capacitance can reduce start up time in the nrf, i got one with 8 pf. I would need to simulate the actual load capacitance since there will be some stray capacitance from the board, so i put in temporary values of 12 pf for each capacitor.
<img width="1477" height="880" alt="Screenshot 2026-08-09 020735" src="https://github.com/user-attachments/assets/7e0fbc38-47a3-490f-89e4-ba8ada4d8a94" />
This is what it looked like in the end.

Next, I refined the dw3210/dw3220 part of the schematic. I added a crystal, dw gpios, and a second antenna. I want this project to support pdoa out of the box, so yeah. 
<img width="546" height="407" alt="Screenshot 2026-08-09 020838" src="https://github.com/user-attachments/assets/1ae70b9f-9908-4121-9f63-e541355a73c7" />
For this crystal, it is the recommended crystal to use according to the manufacturer of the dw3000 chips. So i just used that, same logic with the load capacitors. There is a 1 pf decoup cap for XTO.

<img width="806" height="733" alt="Screenshot 2026-08-09 021107" src="https://github.com/user-attachments/assets/64ac6fcb-c534-40e5-9676-5b98211a709f" />

This is what it looked like in the end.

<img width="959" height="696" alt="Screenshot 2026-08-09 021138" src="https://github.com/user-attachments/assets/05c62399-2196-4e2b-b3cd-b2b184ab78a3" />

I used a dip switch to do analog logic with how i am going to configure each anchor. The first switch will be if pdoa is on or off. If Switch 1 is on, pdoa is enabled, and vice versa.  It is connected to an adc/ain pin of the nrf. I did some research on how the ain pin of the nrf worked. It actually read voltage and had a really high input impedance of 5M, so i needed a 10k pull down resistor to complete the circuit and allow the ain pin to read the voltage. 
The next four switches is to identify the anchor. If pdoa is on, and all other switches are on, the anchor is a master, else, its a slave. Since there are four switches for ID, there is 2^4 amount of possibilities. This allows up to 16 anchors to be placed, which is wayyy more than enough. 

<img width="1221" height="894" alt="Screenshot 2026-08-09 021657" src="https://github.com/user-attachments/assets/e3d912d1-fef9-442e-8e10-323e4bacbae4" />
This is what the rest of the schematic look like.

<img width="1273" height="851" alt="Screenshot 2026-08-09 021729" src="https://github.com/user-attachments/assets/83249ba9-8606-4027-b0b4-97b865467c73" />

This is the entire schematic.

Time spent: 5 hours







