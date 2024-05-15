# PicoUD
A modified version of the popular UD2.7 tesla coil driver to support phase-shifted qcw operation (and some other cool features)

# Board info:

![PicoUD](https://github.com/NebulaXaD/Pico_UD/blob/main/picoud.png)

The upper pot controls the phase lead, the tht one controls the frequency for the startup and phase lead of the coil (very important this one is tuned well, mentioned later why), and the lower one controls ocd.

# Some notes on the design:

If you soldered everything correctly, it should draw 500-750mW idle.

If you have spare ic heatsinks, put one on the 3.3V reg since it gets a bit warm after the board is powered for extended periods.

Technically board supports upto 28V input, but i recommend going 20V and winding a 1:1 gdt (or 24V and 1.2:1 gdt) since theres almost no benefits of going higher, also at high frequencies the board will draw upwards of 75W due to cross conductions of the gdt fets so have a beefy psu powering it (at least 2-3A capable). You can lower the consumption with increasing the 100Ohm gate resistor to 470 or 1k, which makes it draw about 5-10W cw but might result into slower gate rise times so wouldnt recommend this for qcw or other pulsed operations.

Unlike a regular drsstc, here its very important where you place the gdts and how they are oriented, make sure each gdt is assigned to a separate halfbridge and check if the phase shift starts at max or at min - flip a primary of one gdt and it will fix it.

Aim to about 1A of feedback current, but you can go lower for longer pulses / cw (might get implemented later), this circuit is really sensitive on the input side and can lock onto the feedback with only couple of volts across the primary and 1:100 ct in my setup.

Due to how i made the code handle phase shifting, if the angle goes over 180°, it will start to act really unpredictibly and be undstable so to counter that, the code currently only allows you to go up to 165-170° as a safety feature. Luckily the coil is at its highest frequency at the beginning so as the freq drops, the phase shift angle will too so you dont have to worry too much about arc detunning messing with the driver.

The idea was to use a pico W to control everything, but to save on money in case i blow more of them i made the prototype with the basic pico and for the time being, theres an input to control it - it reads the duration of the 3.3v pulse and creates a ramp to match it, for example a 20ms pulse would mean 16ms rise and 4ms fall ramp. -This will be changed in the future.

There is a safety feature that will limit max pulse lenght to 30ms (altho this can be changed), since you wont benefit from longer ramps. 

Altho i recommend shielding the board, i have never had any noise issues running the board right next to the bridge and coil.

# General tips on qcw design:

Connect a 47 - 150nF 2+kV cap from the bottom of the secondary to the negative rail of the bridge, this is will protect it from arc overs and is enough grounding for the circuit to work properly, this is not a joke btw.

While calculating res freq. of your setup, keep in mind that it will ring on the upper pole so your operating freq will actually be around:  fres / sqrt ( 1 - coupling) so try not to make it too high.

For this setup, watch the spikes at the beginning of the pulses across the hard switched igbts and in case they are concerning, put rc snubbers across them. In my case 2s2p of 5.1Ohm 2W resistors (watch the temp of these, i killed a single 2W resistor in an earlier setup with a single pulse!) with a 2kV 4.7nF cap did the job.

If you have any additional questions, or just wanna share your progress, feel free to dm me on discord (same username as here).
