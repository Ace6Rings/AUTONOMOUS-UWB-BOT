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




