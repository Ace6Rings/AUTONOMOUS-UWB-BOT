---
title: Autobot
author: Ace6Rings
description: placeholder
created_at: "8/2/2026"
---


# 8/7


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

Time spent: 4 hours

# 8/8 

I spent 30 minutes on fixing my schematic with my friend who knows how to route. <img width="1187" height="796" alt="Screenshot 2026-08-08 033142" src="https://github.com/user-attachments/assets/e3097999-7415-4dac-9731-b4eecb491232" />

I left sbu1 and 2 floating, added correct capacitor values to the charging ic, added correct capacitor values to the dw3210, fixed the rf configuration since 2pf cap not required for r2, fixed led direction, and a bunch of other stuff.

Time spent: 0.5 hours

# 8/9 

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
# 8/10
I moved on to the robot mainboard pcb today. First, I did a little bit of research on the general parts im going to use and I didn't really do much there.
The main thing I did today is wiring the power circuit. 
I am using the HUSB238 to negotiate the voltage the USBC will deliver, and I configured it so that it gave 12 volts 3 amps (or 3.5, i forgot).
<img width="1128" height="558" alt="Screenshot 2026-08-11 030144" src="https://github.com/user-attachments/assets/6bfd3664-515e-40ee-b5e5-99a5c62f21a1" />
I also added 22 ohm resistors to account for ringing, but it should probably be fine anyway since its usb 2.0
Then, I moved on to the battery charging IC. I chose the BQ24170RGYR. It is a pretty advanced battery charging ic since it can support up to 3 cells and have all those protections and stuff. But that also means there is alot of stuff to wire up. This took the majority of the session time since the documentation wasn't very good at all.
<img width="1056" height="743" alt="Screenshot 2026-08-11 030405" src="https://github.com/user-attachments/assets/6f6f72e6-e046-47c9-85be-451d13104d5f" />
This is the final circuit (besides the values ofc) and it was pretty tedious to get to this point because of reference schematics like this.
<img width="655" height="480" alt="Screenshot 2026-08-10 234656" src="https://github.com/user-attachments/assets/722be356-407b-4385-ac56-47043e8680c1" />
It looks pretty simple after I made the circuit myself but it was a huge mess when I first saw it. 
There are pretty strange values like this
<img width="712" height="466" alt="Screenshot 2026-08-11 002533" src="https://github.com/user-attachments/assets/30549811-a070-4699-8fd9-03a804b6120d" />
I guess they meant microfarad instead of millifarad since other reference circuits made it micro and it just doesnt make sense to have that big of a capacitor anyway.
<img width="685" height="755" alt="Screenshot 2026-08-11 021502" src="https://github.com/user-attachments/assets/74cd7078-3b10-4d44-a6ae-b6640ba88981" />
Finally, I ended up with this LM5176 buck converter. This one is also pretty advanced since theres alot of protections and modes to change the frequency (ig noise) and stuff. This is useful when i make my own filtering later.
This will take some time because every capacitor value isn't fixed. I have to look at the datasheet and do some math to get the capacitor value i want since it does changes settings like bootup time and protection settings.

Time spent: 4.5 hours

# 8/13 

In this session, I worked on the rest of the power schematic for the main robot board.
<img width="1353" height="620" alt="Screenshot 2026-08-15 163355" src="https://github.com/user-attachments/assets/ecbd575c-cc2a-4141-860e-49483464d0af" />
I did some research on how to use this buck converter. Theres some features like dithering that I didn't need and some features like ccm that I do need for my usecase. I used the webench calculator to find the recommended values for the capacitors, inductors, and resistors for this ic so that I don't have to do the math. At the end, I ended up with the schematic you see above.
Then, I did some research for the rest of the power rails im going to have.
I decided that I should split the power from the battery (~12v) into 24v via the lm5175, and 5v via the LM2596. I didn't spend nearly as much time as with the other parts of the power schematic compared to this but I did do a little research on the efficiency curves. The lm2596 has around ~90% efficiency at 12 vin and 5 vout at like 1.5a or 2a. This is pretty much fine and its not like I'm powering anything crazy.
<img width="1353" height="620" alt="Screenshot 2026-08-15 163355" src="https://github.com/user-attachments/assets/0de858a7-408b-45bb-a682-c333c313623c" />
Then the 5 volts is going to go to components that need 5v (prolly or prolly not, depending on what stuff I put on there)
and then the 5v is going to go to different low noise ldo to 3v3. The dw3220 are each going to get their own ldo and their own filtering, and i'll make sure anti resonance doesnt happen (prolly won't anyway).
<img width="1207" height="271" alt="Screenshot 2026-08-15 164206" src="https://github.com/user-attachments/assets/ac8dba3e-761a-4c5e-9939-53c3ef1c2b18" />

Time spent: 3 hours

# 8/15

Today is mainly figuring out the actual components of the board. As of right now, I'm going to use a stm32H7 series chip (footprint tbd), two bmi 270 so i can get accurate positioning and acceleration data, two dw 3220 for polling, two tmc 2209 la t for the motor drivers, four tof sensor modules (it will just little 2.54mm pins on the actual pcb since im going to use a module), and two rotary encoders.

I didn't get to record the entire session since it paused midway, but hopefully journaling is enough.


<img width="1317" height="766" alt="Screenshot 2026-08-15 164720" src="https://github.com/user-attachments/assets/bec849b3-450a-4f3f-9cc5-35f38c7435a4" />

I first did the power for the stm32 and the boot pins and reset. I did some research and it turned out that most of the stuff i have to worry about is firmware, so yeah.

<img width="1258" height="421" alt="Screenshot 2026-08-15 164935" src="https://github.com/user-attachments/assets/66c40265-04d1-4b1c-bc4a-8a9091f70007" />

I then did some research on the bmi270 and decided to use the spi 4 wire option because it is faster than i2c. I have more than enough pins so it doesn't matter. They will both be in separate buses so that data arrives at the same time and i can average them out and stuff.

<img width="1340" height="561" alt="Screenshot 2026-08-15 165105" src="https://github.com/user-attachments/assets/5214c638-23bc-45d3-a2b1-b5c7f3917252" />

I read the datasheet for the tmc2209 and got to this. I had to make the symbol myself since the others were pretty bad.
I configured the tmc2209 so that it gives the stm32 data on the position of the motor via the index pin and also some other diagnostic stuff that i only have to worry about in the firmware. There are probably libraries already so im fine.
The diagnostic pin will give diagnostics ig and pdn uart is just a uart pin. Spread and stdby is marked nc because uart takes over anyway so its fine. MS2 and MS1 are configured in a way that it gives 64 microsteps and its uart id is 2. Usually its a problem if they have the same id but its fine because im using separate uart pins for each, so its all good. 
Now, all i have to do is place down some 2.54mm pins to connect my stuff to my modules. But this is going to be quite difficult on the pcb side since i have to account for the cad at the same time, and i dont even have a design in mind right now. The pcb shape will obviously need to account for the dw3220 antennas (2, or 4) so thats a significant amount of space. The mechanical side is not yet figured out exactly but i will just probably use a nema 14 and a timing belt at a 7.5 gear ratio to get the best torque i can. 
Im also not sure what to do about the power schematic so far since im afraid i will mess something up and the robot is gonna blow up.

Time spent: 2 hours

# 8/16

This session focuses on the digital part of the schematic. Usually I can just slap stuff onto the gpio and be done with it, but I've never used the stm32. 
First, I decided to switch my mcu to the stm32h743zit6 because of its higher clock speed (480 mhz). It does have a lower amount of ram (400kb less) but it should be fine since i won't be using a lidar.
Speaking of lidars, I decided to not use a 2d lidar anymore because the vehicle is going to be low on the ground. There is just no significant advantages to using a lidar over a ToF sensor. 
I chose to use the VL53L1X module from adafruit. I might use their other module with a smaller range in how much distance it measures but can measure shorter distances more accurately. I might just use that because this is meant for obstacle avoiding anyway.
(VL6180X)
Choosing the pins for the stm32 is also a challenge because of the board layout. The board will largely be symmetrical because i need it to be for my bmi 270 and dw3220. It just won't work correctly if it isn't. I did some preliminary wiring.
<img width="771" height="865" alt="Screenshot 2026-08-16 165548" src="https://github.com/user-attachments/assets/655bf509-3fea-416c-90f4-066e9e4e05e4" />
Oh yeah i changed the nreset wiring because it has an internal pull  up.
<img width="892" height="318" alt="Screenshot 2026-08-16 165556" src="https://github.com/user-attachments/assets/2f09c49c-4c56-44c0-ade6-5ab4611d1bad" />
I made my own symbol for the adafruit module and it looks like that. I will organize my schematic later fr
<img width="717" height="840" alt="Screenshot 2026-08-16 165605" src="https://github.com/user-attachments/assets/7ab95571-4389-4afd-b49f-51bf051c6faf" />
After asking on the kicad discord server i decided that i shouldn't use an onboard battery charging module due to safety reasons. It won't improve my performance in anyway so i might aswell omit that and leave it for v2.
<img width="1112" height="446" alt="Screenshot 2026-08-16 165614" src="https://github.com/user-attachments/assets/ab7cf129-96a0-4185-8c98-63c8c3b64461" />
I added a ina226 to do some current sensing and added a shunt resistor. I might change the value but 5m seems good rn.
<img width="665" height="198" alt="Screenshot 2026-08-16 165629" src="https://github.com/user-attachments/assets/b4e661d5-5851-4cdd-9e72-3ce05fb6e3d4" />
And yeah i changed the mcu to this
<img width="900" height="574" alt="Screenshot 2026-08-16 170642" src="https://github.com/user-attachments/assets/6c5fae04-205e-4e37-8da5-6a18a7f83e24" />
<img width="1086" height="683" alt="Screenshot 2026-08-16 170635" src="https://github.com/user-attachments/assets/ee085415-7775-47be-ab3a-5ed287a300a8" />
<img width="1110" height="763" alt="Screenshot 2026-08-16 170624" src="https://github.com/user-attachments/assets/36be733d-4118-42bc-8273-09f8bebb449c" />
This is the entire schematic, i will clean it up later.
Time spent: 2 hours

# 8/19 

This session is about doing a little more research on how the overall project is going to go. First, I decided to look for simpler buck converters to avoid potential issues. I don't want by project to not work just because of some power management issue. However, all the simple buck converters can't provide sufficient power. 
So i decided to go back to the lm5175, mainly because i don't want my previous research and work to go to waste. After some thought, I decided that I should just make the power system on a separate breakout board.
The battery will first go to the breakout board via screw terminals (or whatever port is good, maybe i'll just get a 12v pack but thats chunky and looks unpolished). Then, the power will split to the lm5175 path and the other path will go straight to the 12v terminal. So one board provides both 12v and 24v. This is also optimal for noise because they are physically on separate boards.
I might also put the 5v buck converter here so that the board just outputs 24v and 5v. The 3v3 ldo will be on the mainboard to optimize for noise.

To do this, I first need to confirm my values and parts. So far, I've been slacking a little about part sourcing and values, but this time i need to confirm it.
The lm5175 will be configured for 12v-16v vin (originally 6-16v but 2.6uH inductors are very difficult to source, so 3.3uH is the best option), 24 vout, 10 ms wake up, and ccm on.
Because i changed the settings, the values naturally shift aswell.<img width="1067" height="775" alt="Screenshot 2026-08-19 213743" src="https://github.com/user-attachments/assets/cc85bf75-e53f-48b8-b1d4-17e4a039b868" />
<img width="926" height="672" alt="Screenshot 2026-08-19 213702" src="https://github.com/user-attachments/assets/bc7284cf-89fe-4166-bcf2-b057faccbadb" />
<img width="449" height="311" alt="Screenshot 2026-08-19 213648" src="https://github.com/user-attachments/assets/f7d1f4a8-818b-45d7-9432-549bdd5ea313" />
<img width="613" height="834" alt="Screenshot 2026-08-19 213639" src="https://github.com/user-attachments/assets/dc1ac96c-af97-4543-a3a7-8ab6fe897c71" />
You can see how the values changed and also my parts are there too (except for the mosfets, I will deal with them tomorrow).
Then, I moved onto some preliminary pcb routing. It is still unfinished and unpolished, but here is the progress i made.
<img width="1148" height="657" alt="Screenshot 2026-08-19 213810" src="https://github.com/user-attachments/assets/2f589510-b1ce-456c-b7c7-d0bc62aa43d4" />
I decided to use electrolytic capacitors (smd) because the datasheet says that its recommended.
<img width="822" height="638" alt="Screenshot 2026-08-19 213818" src="https://github.com/user-attachments/assets/f8fe8280-68d7-4c6c-9d08-c9494ef78adb" />
I routed the config pins
<img width="777" height="623" alt="Screenshot 2026-08-19 213826" src="https://github.com/user-attachments/assets/0c04ac19-7733-40be-829d-307a059a8000" />
The mosfets wont be sot23, so i didnt route this yet. The inductor is huge though.<img width="1238" height="651" alt="Screenshot 2026-08-19 213839" src="https://github.com/user-attachments/assets/f44c9251-e9b0-4ead-9354-4f715d54c154" />
Here is the overall board. It is going to be long and skinny. Hopefully this will turn out nicely. I doubt that the UWB performance will drop because of the extra wires, infact, it might be better since theres more space in the main board and less noise (hopefully). <br>

Time spent: 4.6 hours

# 8/20

This session is a continuation of making my breakout board for the power. 
Finding parts was quite a challenge because I am unfamiliar with analog and power circuits. I searched up what parts are recommended and i ended up using the reference parts from Webench calculator. Most of the parts are unavailable in jlcpcb pcba service, so i used the part as a reference instead. I finally got all my parts.
<img width="892" height="483" alt="Screenshot 2026-08-20 225948" src="https://github.com/user-attachments/assets/cd2358fc-854e-4325-abb2-308d9d65143d" />
<img width="789" height="468" alt="Screenshot 2026-08-20 230003" src="https://github.com/user-attachments/assets/ba97c89e-70bc-41b6-a8c6-3b7a29499087" />
<img width="903" height="523" alt="Screenshot 2026-08-20 230016" src="https://github.com/user-attachments/assets/9ee09f84-c79a-4b30-aa26-a64d5ee08d28" />
These are the parts i found, and of course i needed to source the footprints aswell. 
Overall, these didn't take so long. Maybe 1-1.5 hours worth of part sourcing.
The most challenging part was to find the correct inductor, since the rest of the bom depends on this one inductor. I configured the lm5175 so that it uses a 2.2uH inductor. Some inductors are just too big, and some aren't exactly power inductors. I finally found the correct inductor, and it is pretty space efficient too. 
This does mean i have to drop my max current but thats fine as long as i get nema 17 motors (8A max since 2 A per phase and there are 2 bipolar motors).
As for the capacitors, I've heard that polymer capacitors perform much better because of its ESR and ripple current. I don't know too deep about the capacitors, and power isn't the point of this project anyway. 
You might also notice that the picture of the lm5175 is a different package. That is because the qfn version is unavailable and I had to change the pin definitions. That was a little annoying but its fine. 
Now, for the PCB, I looked around the internet for recommended setups (cuz im not a professional). I tried out multiple orientations, but i find that this one works the best.
<img width="1392" height="780" alt="Screenshot 2026-08-20 225519" src="https://github.com/user-attachments/assets/39de73e5-6249-4cbd-af81-cee11c9d4519" />
This one is pretty cool since all the high power stuff is in copper pours and the gate pins are conveniently placed. very cool. 
<img width="461" height="223" alt="Screenshot 2026-08-20 222642" src="https://github.com/user-attachments/assets/33200397-db2d-418f-bb59-615ac11d14c4" />
This part of the pcb wasn't in the previous picture because i didn't quite figure it out yet. I will probably place the diodes in a far away place since these diodes are just for boot pins.
<img width="826" height="772" alt="Screenshot 2026-08-20 225510" src="https://github.com/user-attachments/assets/9ef2e212-1484-42d6-a932-e9e35bcc8a4b" />
I placed some vias for the SW1 and SW2 pins (sw2 not done). Hopefully that is enough to carry alot of current. The package of the lm5175 is certainly a challenge, and i am not sure of the effects of placing the hdrv1 trace so close, but it should be fine since it is only 300khz. They are separated by 2 layers anyway, with a gnd plane right underneath as reference. 
Also i might put the bottom stuff into the 3rd layer since that is what they are for anyway, power. 
I also need to figure out what to do with all the empty space, I can't exactly make it thinner or shorter, but functionally it should be ok for my robot. 

Time spent: 4.75 hours

# 8/21

I continued to route my pcb, it's almost done! I conferred with my friends, who also happens to be making a power supply using a buck boost converter, he gave me some tips like using a LTC2433 instead of using a current sense resistor for the average current sense feature. 
I also found some reference schematics at https://www.ti.com/reference-designs/index.html#search?keyword=LM5175 and https://www.ti.com/lit/df/tidrha4/tidrha4.pdf?ts=1787329435707&ref_url=https%3A%2F%2Fti.com%2F
I didn't really get anywhere far in this session since I was more focused on making my existing routing work and i was stressing over the placements of parts and such. I thought that I should place the decoupling capacitors close, but for this specific package it seems to be impossible.
<img width="905" height="596" alt="Screenshot 2026-08-21 233409" src="https://github.com/user-attachments/assets/bd67009b-a86f-4802-bea3-1ce6157733a7" />
This part of the pcb was what took most of the time today. There is just so much sensitive traces in a such weird order (cuz of the pinout) that it is pretty hard to make this work. Thankfully, I have reference pcbs to help me, but i shouldn't rely on that completely. <img width="332" height="703" alt="Screenshot 2026-08-21 233359" src="https://github.com/user-attachments/assets/d68eba4e-76b0-4f46-9582-9c5d84c115e1" />
I added this to my sw2 pin, technically i don't have to do this thick of a "trace" but it just looks so much nicer. I did some research on how this ic actually works, and it turns out that none of the pins actually have high current, they just sense stuff and switch the mosfets. <img width="1153" height="422" alt="Screenshot 2026-08-21 233632" src="https://github.com/user-attachments/assets/3056aa7c-7368-4e6c-b0b5-e1bb3f8b13ca" />
Speaking of mosfets, this is the routing that i did. Its okay ig.
<img width="1255" height="685" alt="Screenshot 2026-08-21 233351" src="https://github.com/user-attachments/assets/cd3af6a0-b944-45b2-88fc-587578fbe619" />
This is the entire pcb rn. I could just route the rest lazily and move along and it will still work (albeit prolly not well), but i want this to be as polished as it can be. 

Time spent: 2.5 hours


# 8/22 

For this session i finished the breakout board pcb routing and did half of the anchor pcb. 
For the breakout board i did some additional research on how to ground the thing and also the copper planes on the first layer.
<img width="1151" height="698" alt="Screenshot 2026-08-23 022549" src="https://github.com/user-attachments/assets/87670404-9b13-4acb-9560-641af585a9bb" />
To connect pgnd and agnd the datasheet says to connect them in the ic pad, which i did. There was some issues with getting the nets right.
<img width="1296" height="789" alt="Screenshot 2026-08-23 022557" src="https://github.com/user-attachments/assets/69dbee62-bfd0-4903-a609-aab1b9dd0906" />
From the two pictures, you can see that there is a small agnd plane and a bigger gnd plane for the rest of the bottom board. They are connected (will be after i get my via stitching plug in) using vias which stitches the 1st layer gnd plane to the 2nd layer gnd plane (unbroken). 
<img width="1265" height="677" alt="Screenshot 2026-08-23 022606" src="https://github.com/user-attachments/assets/5a7fad89-0966-4f19-8e3c-d9fa9bfd80eb" />
This is the final (not really) look of the pcb. There is definitely some improvements to be made but i want to focus on the real project instead of some power system. 
I planned to continue my mainboard schematic but decided that i should do the anchor pcb first, especially since the schematic is actually well polished and finished (except for some footprints).<img width="1011" height="728" alt="Screenshot 2026-08-23 015253" src="https://github.com/user-attachments/assets/5be58dee-67e5-4a3f-9fb3-6e11432e552a" />
I first used user drawings to draw out the shape of the pcb, this is 40mm by 75 mm. I centered everything using some user drawings aswell to make this as symmetrical as i can. This is because of the spi traces you see on the top of the nrf. 
<img width="754" height="745" alt="Screenshot 2026-08-23 023505" src="https://github.com/user-attachments/assets/661e62d1-eb11-4661-95ca-0fb6cd637f89" />
You may also notice some pin header footprints on either side of the board. These are used to flash the chip, debug the chip, and also some extra gpio pins that i may or may not use after i build it (like adding a imu to detect if the anchor has been moved). In any case, these pin headers should be useful to me and also just makes the board much more useful for others to build off of. 
You may also notice that there are rounded traces for my rf trace. This is because im going to run this at ~8ghz but it can be like 7-9. I forgot the exact figures. There is also two dc blocker capacitors (0201) and im going to put a gnd plane on the first layer and stitch it. That way, everything should be ok. 

Time spent: 4.2 hours

8/23

For this session i continued working on the anchor pcb. Most of the time was spent trying to figure out how to do the decoupling capacitors for the dw3220. I can tell that i will spend quite some time to do the power system for the nrf52840 aswell. This is because i enabled the dc dc buck converter in the ic, which increases efficiency but adds a few components i need to use. Since i am aiming for having single side pcba, it is pretty difficult to optimize the placement. The crystal was a nuisance since it is big and also have external load capacitors. Conveniently, they are also next to the power pins i'm trying to decouple.
<img width="802" height="727" alt="Screenshot 2026-08-23 235816" src="https://github.com/user-attachments/assets/007c1237-9f29-4cca-894a-aabaf71210d6" />
After a few tries, i think it turned out pretty good, i dont think i can optimize this further without changing the parts and footprints. Why not 0201? because that causes pcba to be much more expensive allegedly (didnt test).
I also used rounded corners to minimize reflection and stuff. The cyrstal is especially important, probably the most important component on the board besides the antennas. This is because i need precise clock measurements because of what i am doing with it (you should know from the context of this project).
According to the application notes, it seems to recommend a specific crystal and requests a 3.3 pf load capacitor. I will look more into it tomorrow. Also, there is stuff about band pass filters. I will look into that later aswell since it might be useful/required for the project. This could improve performance i guess.
But anyway, I didnt work on the actual rf portion of the pcb today. I did literally everything else except for the nrf antenna, i might not even use it but i guess i can give it a try (its just inconvenient to route)<img width="1123" height="532" alt="Screenshot 2026-08-23 235413" src="https://github.com/user-attachments/assets/95e8f1c4-b92c-4584-bb08-64097f8a34b9" />
<img width="933" height="695" alt="Screenshot 2026-08-23 235423" src="https://github.com/user-attachments/assets/34960bd2-6c34-4ddd-b7d4-81784c85aa0d" />
So far, its looking good. 
I just need to find the inductors and dipswitch i want and everything will be complete. <br>
Time spent: 2.67 hours











