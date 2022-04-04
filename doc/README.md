Just some documentation on the Entex Adventure Vision itself.

The following is from --> [https://web.archive.org/web/20160427192034/http://www.m-e-g-a.org/adventurevision-hardware/](https://web.archive.org/web/20160427192034/http://www.m-e-g-a.org/adventurevision-hardware/)

# Hardware Specifications

* CPU : Intel 8048 @ 11Mhz (733.333 Kips performance) (733 Kips <= 11Mhz divided by 15)
* Sound : [National Semiconductor COP411L](https://web.archive.org/web/20160428083625/http://www.m-e-g-a.org/adventurevision-apu/) @ 52.632 kHz - Clock is generated by unaccurate RC circuit (~13% error)
* RAM: 64 bytes (8048 internal), 1KB (on main PCB)
* ROM: 1KB (BIOS, 8048 internal), 4KB (Cartridge)
* Input: 4 direction joystick, 4 buttons duplicated on each side of the joystick
* Graphics: 150×40 monochrome pixels

## Memory map

### ROM
| `Port 1 =>` | `P1[2] = 0`         | `P1[2] = 1`         |
| ----------- | ------------------- | ------------------- |
| **Address** | **Description**     | **Description**     |
| $000-$3FF   | BIOS                | Cartridge $000-$3FF |
| $400-$FFF   | Cartridge $400-$FFF | Cartridge $400-$FFF |

### XRAM (External RAM)

| **Address** | **Description**                 |
| ----------- | ------------------------------- |
$000-$0FF     | General Storage, 256 Bytes      |
$100-$1FF     | Video Bank 1 (Column #1-#50)    |
$200-$2FF     | Video Bank 2 (Column #51-#100)  |
$300-$3FF     | Video Bank 3 (Column #101-#150) |

### I/O Port 1

#### Bit 0-1 : RAM bank switch
These pins select one of the four 256 banks that external RAM should use. 

#### Bit 2 : BIOS disable
These pin is used to disable or enable the internal BIOS.  
When set to 0 the internal BIOS ROM is enabled from $000-$3FF.  
When set to 1 the beginning of the cartridge ROM appears at $000-$3FF.

#### Bit 3-7 : Controller
These pins receive the controller status, inputs are active low.  
Note: For inputs to work, the pins need to be outputted with logic 1.  
Stick can’t do diagonals.

| Input       | `P1[7]` | `P2[6]` | `P2[5]` | `P2[4]` | `P2[3]` | Combinations |
| ----------- | ------- | ------- | ------- | ------- | ------- | ------------ |
| Button 1    | 1       | 1       | 0       | 0       | 1       | Down + Up    |
| Button 2    | 1       | 0       | 1       | 0       | 1       | Down + Right |
| Button 3    | 1       | 1       | 1       | 1       | 0       | Action       |
| Button 4    | 0       | 1       | 1       | 0       | 1       | Down + Left  |
| Stick Down  | 1       | 1       | 1       | 0       | 1       | Down         |
| Stick Up    | 1       | 1       | 0       | 1       | 1       | Up           |
| Stick Right | 1       | 0       | 1       | 1       | 1       | Right        |
| Stick Left  | 0       | 1       | 1       | 1       | 1       | Left         |

### I/O Port 2

#### Pins 0-3
These pins are wired into the address A8 to A11 for cartridge access.

#### Pin 4
When this pin is set to a logic 1, the LED storage registers are latched into the LED output drivers for a synchronous display change. This pin must be set to 0 before writing to the LED storage registers.

#### Pins 5-7
A read from external RAM causes the following: 

| `P2[7]` | `P2[6]` | `P2[5]` | Description                        |
| ------- | ------- | ------- | ---------------------------------- |
| 0       | 0       | 0       | None                               |
| 0       | 0       | 1       | LED #1-#8 storage register write   |
| 0       | 1       | 0       | LED #9-#16 storage register write  |
| 0       | 1       | 1       | LED #17-#24 storage register write |
| 1       | 0       | 0       | LED #25-#32 storage register write |
| 1       | 0       | 1       | LED #33-#40 storage register write |
| 1       | 1       | 0       | COP411 reset register write        |
| 1       | 1       | 1       | not used/unknown                   |

## Sound
Pins 4-7 are also connected to the COP411 inputs and are used to transfer two sound command nibbles over these lines when the COP411 reset is released. A read from external RAM with P2 at $C0 and data bit D0 set to ’1′ will release the soundchip from reset.  
For more in depth information refer to Sound.

## Graphics
Pin 5 to 7 select the LED rows group to be written on external RAM access.  
When pin 4 is set to high, all the LED registers are latched all at once into the LED bar.  
For more in depth information refer to Graphics. 

The following is from --> [https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-cpu/](https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-cpu/)

# CPU (Intel 8048)

8048 is a 8-bits microcomputer and part of MCS-48 family but only this chip is used in the AdventureVision.  
Oscillator is divided by 15, /3 Counter on the osc circuit and 5 states on each instruction clock.

## Chip specifications


* Intel’s advanced N-channel silicon gate HMOS process.
* Internal ROM: 1K x 8 ROM (One-time programmable?)
* Internal RAM: 64 x 8 RAM
* 8-bits timer/counter
* 27 I/O lines:
    * Port 1, 8-bits IO
    * Port 2, 8-bits IO
    * BUS, 8-bits IO
    * T0, 1-bit Input only
    * T1, 1-bit Input only
    * INT, 1-bit Input only
* All instructions are 1 or 2 cycles

## Adventure Vision interface

### BUS
All 8 pins are connected to the socket and external RAM, using BUS instructions has no real meaning and should be avoided.

### Port 1
These pins are connected to the [banking system](https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-hardware/) and [controllers input](https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-hardware/).

### Port 2
There pins are connected to the [Graphics](https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-graphics/), [Sound](https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-apu/) and most-significant bits of the socket’s address.

### INT Pin
INT is unconnected, always read as logic 1.

### T0 Pin
T0 is connected to the expansion port, there was never any expansion planned so can be assumed to be always logic 1.

### T1 Pin
T1 is connected to the mirror’s sensor, it oscillate at a rate of approx. 15 times per sec.
Logic transition from 1 to 0 indicate that the image is ready to be drawn.
For more information check [Graphics](https://web.archive.org/web/20160427172940/http://www.m-e-g-a.org/adventurevision-graphics/).

### CPU hazards
Program counter increments by 1 in each fetch but A11 is not incremented.  
So if code needs to cross between $7FF to $800, it needs to **SET MB1** and **JMP $800** instead or else the counter will warp-around to $000.  
Program counter can cross between banks 0 to 7 and 8 to 15 freely but can still affect certain instructions implementations such as ones that take into account the lowest 8-bits of the PC: **MOVP A**, **@A**, **JMPP @A** and all conditional jumps.

**SET MB0** and **SET MB1** only take effect on **JMP addr** and **CALL addr**.
During interrupt, A11 is always output as logic 0. (Not important for AdventureVision but is always good to know)

## Programming tricks

### Array/Table read
Instruction **MOVP A, @A** can be used to read tables between the current page from the ROM area.

### Graphics data
Because graphics only start at offset 6 in each bank you can write:
```asm
.org (bank address)
movp a, @a
ret
.db 0, 0, 0, 0  ; for padding
(Bank graphics here)
```

And send the whole image bank with:
```asm
mov r0, 0
loopback:
mov a, r0
call (bank address)
mov @r0, a
djnz r0, loopback
```

### Up to 252 Bytes of data
Table code:
```asm
.org (bank address)
add a, 4
movp a, @a
ret
(Data content here)
```

To read table:
```asm
 ; Table offset on accumulator
call (bank address)
 ; Now the accumulator hold the data
```

### Up to 254 Bytes of data
Table code:
```asm
.org (bank address)
movp a, @a
ret
(Data content here)
```

To read table:
```asm
 ; Table offset on accumulator
add a, 2
call (bank address)
 ; Now the accumulator hold the data
```

### 255 Bytes of data
Table code:
```asm
.org (bank address)
(Data content here) ; Must be exactly 255 bytes
movp a, @a    ; at address $xFF
ret           ; first byte of the next bank
```

To read table:
```asm
 ; Table offset on accumulator
call (bank address) + 255
 ; Now the accumulator hold the data
```

### 256 Bytes of data
For reading 256 bytes data it’s only possible by disabling BIOS and using **MOVP3 A, @A** instruction, data content must be in `$300~$3FF`.
```asm
.org $300
(Data content here)
```

To read table:
```asm
 ; Table offset on accumulator
movp3 a, @a
 ; Now the accumulator hold the data
 ```

## Vector tables
This can be useful to implement states (such as AI).

### Up to 127 vectors
Vector table:
```asm
.org (bank address)
inc a
jmpp @a
(list of vectors with JMP addr instruction)
```

To call the vector:
```asm
 ; Vector index on accumulator
jmp/call (bank address)
```

### 128 vectors
This is archived by using **JMPP @A** just 1 byte before the desired bank address.
Vector table:
```asm
.org (bank address) - 1
jmpp @a    ; Must be in $xFF
(list of vectors with JMP addr instruction)
```

To call the vector:
```asm
 ; Vector index on accumulator
jmp/call (bank address)
```

## Downloads
* [MCS-48 Datasheet (Contains all 8048 information)](https://web.archive.org/web/20160427172940/https://mega.folderflex.com/websharedownload/MEGA/Adventure+Vision/file/608992)
* [MCS-48 Datasheet (Older version, bigger file)](https://web.archive.org/web/20160427172940/https://mega.folderflex.com/websharedownload/MEGA/Adventure+Vision/file/608993)

The following is from --> [https://web.archive.org/web/20160428083625/http://www.m-e-g-a.org/adventurevision-apu/](https://web.archive.org/web/20160428083625/http://www.m-e-g-a.org/adventurevision-apu/)

# APU (National Semiconductor COP411L)

COP411 is a low cost 4-bits microcontroller.  
Clocking is divided by 4, four states per instruction clock.  
In AdventureVision the CKI is connected to a RC circuit (Res 82K, Cap 56pF) which provides approximately 217.770 kHz with ~13% error, this gives about 54.442 kips

## Chip specifications

* Single-Chip N-Channel Microcontroller
* Internal ROM: 512 x 8 (One-time programmable?)
* Internal RAM: 32 x 4
* 19 I/O lines:
    * Port L, 8-bits IO
    * Port G, 4-bits IO
    * Port D, 4-bits Output
* All instructions are 1 or 2 cycles

## AdventureVision interface

### Port L
Lowest 4 pins are connected to the CPU Port 2 highest 4 bits.
It’s used for sound command transmission after reset.
The rest of the pins are left unconnected.

### Port G
Lowest bit of Port G is connected to the audio output circuit, it has higher influence on output.
The rest of the pins are left unconnected.

### Port D
Lowest bit of Port D is connected to the audio output circuit, it has lower influence on output.
The rest of the pins are left unconnected.

### Reset
Connected to a circuit inside the video module, for more information check "Sound transmission".

### Sound transmission
COP411 lacks interrupts support so one solution was to use reset signal in each time the game needs to send a sound command for playing sound effects.
Internal RAM is not cleared on reset, this is important for Command 0
The CPU will write $C0 into Port 2 and access external RAM with lowest data bit of 0 then 1 causing a reset into the chip.
CPU will delay and send the higher nibble first into Port 2 higher bits, delay some more and send the lower nibble into Port 2 higher bits.
Now the APU will process the command.

### Sound commands
[BIOS $1B](https://web.archive.org/web/20160428083625/http://www.m-e-g-a.org/av_bios.html) can be used to transmit sound commands.
Highest nibble is the command, lower nibble is the data.
Transmitting a new command will cancel the sound that is currently being played, command 0 can be used for silence.
Tones are played with 2 segments but SFX are played with only 1.
The volume of each segment can be changed on command 0

### Command 0
#### Control Register
Bit 0 control the looping: 0 will play once, 1 will play infinitely until another command is received.
| Bit 2 | Bit 1 | 1st Seg. Vol | 2nd Seg. Vol |
| ----- | ----- | ------------ | ------------ |
| 0     | 0     | Low          | Low          |
| 0     | 1     | High         | Low          |
| 1     | 0     | High         | High         |
| 1     | 1     | High         | High         |

Bit 3 control the delay of both segment: 0 plays slow, 1 plays fast

### Command 1
#### Noise generator
Plays noise constantly.
*Loop effect:* Always on, ignores looping control.

### Command 2
#### Slide high to low square-wave pitch
Pew Pew Pew! Commonly used when players shoot.

### Command 3
#### Play noise in 5 different pitches
Simulates a loud big explosion.
*Loop effect:* Sound plays Command 2 when explosion finishes.

### Command 4
#### Slide low to high square-wave pitch
Commonly used when player receive a reward.

### Command 5
#### Slide low to high noise pitch and duration
Thrusters at maximum power, prepare for lift off!
*Loop effect:* At end, sound continue to play from the last pitch change.

### Command 6
#### Slide high to low noise pitch and duration
Can either simulate a long lasting explosion or ship landing when used with command 5.
*Loop effect:* Always off, ignores looping control.

### Command 7
#### Slide medium to low square-wave pitch
The enemy is shooting at us! Execute evasive maneuvers!

### Command 8
#### Very quickly slide low to high square-wave pitch
Multiple purpose.
*Loop effect:* Hold on, someones ringing on the phone…

### Command 9
#### Quickly slide low to high square-wave pitch
What kirby is going by jumping around inside AdventureVision!?
*Loop effect:* Someone set us up the alarm!

### Commands A, B, C and D
#### Unknown
Sometimes it changes pitch, other times it clears the control, not much is known.

### Command E and F
#### Tone playing
Plays square-wave with a specific note, due to very low accuracy of the APU clock the note will sound off-tone.

| Value | Note+Octave |
| ----- | ----------- |
| 0     | A# 3        |
| 1     | B 3         |
| 2     | C 4         |
| 3     | C# 4        |
| 4     | D 4         |
| 5     | D# 4        |
| 6     | E 4         |
| 7     | F 4         |
| 8     | F# 4        |
| 9     | G 4         |
| 10    | G# 4        |
| 11    | A 4         |
| 12    | A# 4        |
| 13    | B 4         |
| 14    | C 5         |
| 15    | C# 5        |

The following is from --> [https://web.archive.org/web/20160428041443/http://www.m-e-g-a.org/adventurevision-graphics/](https://web.archive.org/web/20160428041443/http://www.m-e-g-a.org/adventurevision-graphics/)

# Graphics

## Video circuit
Video circuit consists of:

* **Motor** – A 450rpm tape recording motor which spins 7.5 times per sec
* **LEDs bar** – A bar with 40 red LEDs
* **Plastic plate with 2 mirror films** – This reflects the light from the bar into the screen
* **Sensor** – Senses when the mirror is positioned at the first pixel’s column

### Wobbling effect
AdventureVision suffers from a effect called "Wobbling", the 2 sides of the mirror doesn’t reflect the light in the same way making the image to have different perspective compared to the opposite side.

### Displaying graphics with BIOS
The easiest way to display graphics on screen is to write the image into external RAM and calling [BIOS $03](https://web.archive.org/web/20160428041443/http://www.m-e-g-a.org/adventurevision-bios/) to display the content into the screen.  
Each bank in external RAM is 50×40 pixels, starting from Bank 1 (leftmost) to 3 (rightmost), Bank 0 is only used for game data.  
Each block start at offset $06 toward $FF, 5 bytes (40 pixels) in each column, left to right.  
Each column is displayed from bottom to top, with the least significant bit on the bottom, the LEDs are active low (logic 0 will lit the LED).  
On calling [BIOS $03](https://web.archive.org/web/20160428041443/http://www.m-e-g-a.org/adventurevision-bios/), the routine will wait for the sensor to toggle and start displaying the image into the LED bar, one whole column at a time by writing the data into the storage registers and latching them all at once, each column takes 44 cycles (61 cycles when crossing banks). The routine returns when all 150 columns were displayed into the screen.  
The image needs to be always refreshed to archive the human "perseverance of vision" effect.

The following is from --> [https://web.archive.org/web/20160428101903/http://www.m-e-g-a.org/adventurevision-bios/](https://web.archive.org/web/20160428101903/http://www.m-e-g-a.org/adventurevision-bios/)

# BIOS

## BIOS vector table

| Vectoor  | Address    | Label      | Description                                                                                 |
| 00       | 000        | RESET      | Start game cartridge                                                                        |
| 03       | 036        | DISPLAY    | Display the current screen in VRAM                                                          |
| 05       | 071        | DRAW       | Draw sprite in VRAM with VRAM collision detection                                           |
| 07       | 0F0        | DRAWP      | Draw sprite in VRAM with fixed point collision detection                                    |
| 09       | 1A0        | DOSPRITES  |                                                                                             |
| 0B       | 2B9        | STEP       | Adjust sprites x-step and y-step properties and adjust X and Y displacements accordingly    |
| 0D       | 361        | MOVEX      | Move sprite A pixels right(A>=0) or left(A<0)                                               |
| 0F       | 324        | MOVEY      | Move sprite A pixels up(A>=0) or down (A<0)                                                 |
| 11       | 2E2        | CLRXRAM    | Fill bank 0 with 00 and banks 1-3 with FF                                                   |
| 13       | 2EF        | CLS        | Fill VRAM (banks 1-3) with FF so all LEDs are off on display                                |
| 15       | 02B        | CLRIRAM    | Fill internal CPU RAM 20-3F with 00                                                         |
| 17       | 39A        | SCORE      | Add BCD number in A to BCD word at 33(MSB) and 34 (LSB)                                     |
| 19       | 3CE        | GAMEOVER   | displays screen in VRAM until button 3 is pressed and restarts game cartridge               |
| 1B       | 3A9        | SOUND      | Write sound command                                                                         |
| 1D       | 2FF        | GETSPRITE  | Get Y shift, VRAM bank and VRAM pointer from sprite structure                               |
| 1F       | 30E        | SETSPRITE  | Set Y shift, VRAM bank and VRAM pointer of sprite structure                                 |
| 21       | 31D        | SETSPRITEB | Set Y shift, VRAM bank and VRAM pointer of sprite structure and (buggy) clear x and y steps |
| 23       | 197        | TEXT       | Uses fixed sprite structure to draw (text) graphics like Title, GameOver, Score             |
| 25       | 3F1        | BITMASK    | Shift left 1 by R5 times and store result in A (A = 1 ^ R5)                                 |
| 27       | 3EF        | WREXP5     | Write least significant nibble of A to expansion port 5                                     |
| 29       | 3ED        | WREXP4     | Write least significant nibble of A to expansion port 4                                     |

## BIOS functions

### 00 – RESET
#### Start game cartridge
Jump to $800 in the cartridge  
Address: @000  
Modified:  
MB ⇒ 1  
PC ⇒ @800  

### 03 – DISPLAY
#### Display the current screen in VRAM
Wait for the sensor to trigger and render all content in XRAM bank 1 to 3 into the screen  
Address: @036  
Modified:  
RB ⇒ 1  
A ⇒ 0  
P1 ⇒ 0  
P2 ⇒ 0  
R0 in RB0 ⇒ 0  
R1 in RB0 ⇒ 4  

### 05 – DRAW
#### Draw sprite in VRAM with VRAM collision detection
Address: @071  
TODO

### 07 – DRAWP
#### Draw sprite in VRAM with fixed point collision detection
Address: @0F0  
TODO

### 09 – DOSPRITES
#### TODO
Address: @1A0  
TODO

### 0B – STEP
#### Adjust sprites x-step and y-step properties and adjust X and Y displacements accordingly

Case Sprite X-step:  
```
> 0: ⇒ X-step = X-step-1, R2 = R2 + 1, A=$00
< 0: ⇒ X-step = X-step+1, R2 = R2 – 1, A=$00
```

Case Sprite Y-step:  
```
> 0: ⇒ Y-step = Y-step-1, R3 = R3 + 1, A=$00
< 0: ⇒ Y-step = Y-step+1, R3 = R3 – 1, A=$00
Address: @2B9
```


Entry:
```
R1 ⇐ pointer to sprite structure [range: $00-$F8]
R2 ⇐ X displacement [range: $00-$FF]
R3 ⇐ Y displacement [range: $00-$FF]
```

Modified:
```
R1 ⇒ points to variable property of sprite structure
R2 ⇒ X displacement
R3 ⇒ Y displacement
A ⇒ $00 at least one of the displacements changed $FF: none of the displacements changed (X-step and Y-step where both 0)
```

### 0D – MOVEX
#### Move display position of sprite A pixels to the left or right.
When the new position is <0 or >149 it will wrap around. A variable at IRAM location 2E is read to determine the last VRAM bank.  
This variable (IRAM[$2E]) must be initialized before using this function. It can be set to 3: for wrap at position >149 2: Wrap at position >99 or 1: wrap at position 49.
```
Address: @361
Entry:
A ⇐ X displacement [range: -1 to -128 move left, 0-127 move right]
R6 ⇐ sprite VRAM bank.
R7 ⇐ sprite VRAM pointer.
Modified:
R6 ⇒ new sprite VRAM bank.
R7 ⇒ new sprite VRAM pointer.
R0
```

### 0F – MOVEY
#### Move sprite A pixels up(A>=0) or down (A<0)
Add vertical displacement of A pixels to the Y coordinate pointed to by Y-shift, VRAM bank and VRAM pointer values.  
When the new coordinate gets out of the range 0-39 it will wrap around to the next or previous column rather then the same column.  
This effect can be be misused to move the coordinate in both the X and Y directions simultaneously.  
IRAM location [$2E] Last VRAM page must be set to 3 prior to using this function for the wrapping mechanism to function correctly.

```
Address: @324
Entry:
A ⇐ Y displacement in pixels [range: 0 to 39 move up, -1 to -39 move down]
R5 ⇐ Y-shift
R6 ⇐ VRAM bank
R7 ⇐ VRAM pointer
IRAM [$2E] ⇒ Last VRAM bank [range: 1-3]
Modified:
R0 ⇒ $2E
R5 ⇒ new Y-shift
R6 ⇒ new VRAM bank
R7 ⇒ new VRAM
A
```

### 11 – CLRXRAM
#### Fill bank 0 with 00 and banks 1-3 with FF
Fill all XRAM bank 0 with $00 and banks 1 to 3 with $FF  
Clears extra ram and turn off all LEDs in vram.
```
Address: @2E2
Modified:
A ⇒ 0
P1 ⇒ 0
R0 ⇒ 0
R3 ⇒ 0
```

### 13 – CLS
#### Fill VRAM (banks 1-3) with $FF so all LEDs are turned off on display
XRAM banks 1 to 3 are all filled with $FF Which turns off all the LEDs when VRAM is output to display.
```
Address: @2EF
Modified:
A ⇒ 0
P1 ⇒ 0
R0 ⇒ 0
R3 ⇒ 0
```

### 15 – CLRIRAM
#### Fill internal CPU RAM 20-3F with 00
Clear internal RAM from $20 to $3F for initialization
```
Address: @02B
Modified:
A ⇒ 0
R0 ⇒ 1F
R1 ⇒ 0
```

### 17 – SCORE
#### Add BCD number in A to BCD word
The BCD value in A is added to 16-bit BCD word stored at IRAM $33~$34. IRAM location $34 holds the LSB of the 16-bit word and IRAM location $33 the MSB of the 16-bit word. This function is used to increase the score.
```
Address: @39A
Entry:
A ⇐ BCD value [range: BCD($00-$99)]
Modified:
A ⇒ no overflow: least significant BCD digits (1s and 10s) else: most: significant BCD digits (100s and 1000s).
R0 ⇒ no overflow: $34 else $33
IRAM [$34] ⇒ BCD (IRAM [$34] + A)
IRAM [$33] ⇒ no overflow: no change else: BCD (IRAM [$33] + 1)
```

### 19 – GAMEOVER
This function draws a text sprite and keeps displaying VRAM until button 3 is pressed. After the button is pressed the cartridge is restarted.  
The Y-shift, VRAM bank and VRAM pointer values in R5, R6 and R7 respectively are stored at the sprite structure at XRAM bank 0 addr $00. This sprite structures graphics pointer property is set to 0 and therefore the sprite graphic must be 256-byte aligned to be displayed correctly.
```
Address: @3CE
Entry:
R5 ⇐ Y-shift [range: 0-7]
R6 ⇐ VRAM bank [range: 1-3]
R7 ⇐ VRAM pointer [range: $6-$FF]
Modified:
MB ⇒ 1
PC ⇒ @800
```

### 1B – SOUND
#### Write sound command
Sends sound command in R1 to COP411 sound chip
```
Address: @3A9
Entry:
R1 ⇐ Sound command
Modified:
R0 ⇒ 1
R2 ⇒ 0
P1 ⇒ 0
P2 ⇒ 0
A ⇒ 0
```

### 1D – GETSPRITE
#### Get Y shift, VRAM bank and VRAM pointer from sprite structure
R1 points to a 8-byte aligned sprite structure and it’s Y-shift, VRAM bank,and VRAM pointer are read into R5,R6 and R7 respectively.  
The pointer to the sprite structure may point to any of the sprite structures properties but usually it points to the graphics pointer property.  
Note that before calling this function, XRAM bank must be set to 0
```
Address:@2FF
Entry:
R1 ⇐ pointer to sprite structure [range: $00-$F8]
Modified:
R1 ⇒ points to sprite structures VRAM pointer. [(R1 & $F8) + $04]
R5 ⇒ Sprite Y-shift
R6 ⇒ Sprite VRAM bank
R7,A ⇒ Sprite VRAM pointer
Carry ⇒ cleared
```

### 1F – SETSPRITE
#### Set Y shift, VRAM bank and VRAM pointer of sprite structure
Y-shift, VRAM bank and VRAM pointer values in R5,R6 and R7 respectively are stored at the 8-byte aligned sprite structure pointed to by R1.  
The sprite structures pointer may point to any of the sprite structures properties but usually it points to the graphics pointer property.  
Note that before calling this function, XRAM bank must be set to 0
```
Address: @30E
Entry:
R1 ⇐ pointer to sprite structure in XRAM bank 0 [range: $00-$F8]
R5 ⇐ Sprite Y-shift [range: 0-7]
R6 ⇐ Sprite VRAM bank [range: 1-3]
R7 ⇐ Sprite VRAM pointer [range: $00-$FF]
Modified:
R1 ⇒ points to sprite structures VRAM pointer. [(R1 & $F8) + $04]
A ⇒ R7 (Sprite VRAM pointer)
Carry ⇒ cleared
```

### 21 – SETSPRITEB
#### Set sprite and clear x-step and y-step properties (buggy)
This function calls SETSPRITE and was supposed to clear the sprites x-step and y-step properties. Unfortunately a INC R1 instruction was forgotten resulting in clearing the sprites VRAM pointer and X-step properties instead.
```
Address: @31D
Entry:
R1 ⇐ pointer to sprite structure in XRAM bank 0 [range: $00-$FF]
R5 ⇐ Sprite Y-shift [range: 0-7]
R6 ⇐ Sprite VRAM bank [range: 1-3]
R7 ⇐ Sprite VRAM pointer [range: $00-$FF]
Modified:
R1 ⇒ points to sprite structures X-step. [(R1 & $F8) + $05]
A ⇒ 0
Carry ⇒ cleared
```

### 23 – TEXT
This function sets sets R1 itself and writes R0 to the sprite structure at $E0. It calls @30E (1F) and @071 (05) itself
```
Address: @197
Entry:
R0 ⇐ Initial data pointer (as far as I care, index of sprite as used by @802)
R5 ⇐ Y-shift value [0-7]
R6 ⇐ RAM bank to start writing to (((5 * x + y // 8) & $0300) >> 8)
R7 ⇐ RAM pointer ((5 * x + y // 8 + bank – 1) & $ff)
Modified:
@$E1 ⇒ R0
See DRAW
```

### 25 – BITMASK
#### Shift left 1 by R5 times and store result in A
This function converts the Y-shift value to a bit mask which can be used to clear a pixel (ORing) or set a pixel (complementing then ANDing) with VRAM.
```
Address: @3F1
Entry:
R5 ⇐ Y-shift value [0-7]
Modified:
A ⇒ 1 << R5
R1 ⇒ 0
```

### 27 – WREXP5
#### Write to expansion port 5
Write low nibble in A to port 5 of the 8243 Port expander and clears high nibble of A.  
This function is of no use for the Adventure Vision unless an external interface is connected to the expansion slot with a 8243 Port expander.  
It is unknown if there ever existed such an interface at all.
```
Address @3EF
Entry:
PORT5 ⇐ A & $0F
Modified:
A ⇒ A & $0F
```

### 29 – WREXP4
#### Write to expansion port 4
Write low nibble in A to port 4 of the 8243 Port expander and clears high nibble of A.  
This function is of no use for the Adventure Vision unless an external interface is connected to the expansion slot with a 8243 Port expander.  
It is unknown if there ever existed such an interface at all.
```
Address @3ED
Entry:
PORT4 ⇐ A & $0F
Modified:
A ⇒ A & $0F
```

The following is from [https://web.archive.org/web/20160424020222/http://www.m-e-g-a.org/adventurevision-cartridge/](https://web.archive.org/web/20160424020222/http://www.m-e-g-a.org/adventurevision-cartridge/)

# AdventureVision cartridges

All four original AdventureVision cartridges only consist of a single chip without any pcb around it. It is a 4 kilobytes 2532 Eprom bent around a molex connector which is clipped into a plastic housing.

## 2532 Eprom

### Chip features

* Organization: 4096 x 8
* Single +5V Power Supply
* All Inputs/Outputs Fully TTL Compatible
* Static Operation (No Clocks, No Refresh)
* Max Access/Min Cycle Time: 300ns
* 8–Bit Output for Use in Microprocessor Based Systems
* N–Channel Silicon–Gate Technology
* 3–State Output Buffers
* Low Power Dissipation:
    * Active – 400mW Typical
    * Standby – 100mW Standby
* Guaranteed DC Noise Immunity with Standard TTL Loads
* No Pull–Up Resistors Required

### Pinout diagram
The 2532 Eprom pinout is as follows:
```
        _________
 A7 [ 1|   \_/   |24] Vcc
 A6 [ 2|         |23] A8
 A5 [ 3|         |22] A9
 A4 [ 4|         |21] Vpp
 A3 [ 5|    2    |20] PD/nPGM
 A2 [ 6|    5    |19] A10
 A1 [ 7|    3    |18] A11
 A0 [ 8|    2    |17] Q8
 Q1 [ 9|         |16] Q7
 Q2 [10|         |15] Q6
 Q3 [11|         |14] Q5
Vss [12|_________|13] Q4
```

## Using Eeproms (28C64)
The most common and available type of eeprom with at least 4kb is the 8kb 28C64, mostly as 28C64A, 28C64B, 28C64C or 28C64D.

### Chip features

* Fast Read Access Time—150 ns
* CMOS Technology for Low Power Dissipation
    * 30 mA Active
    * 100 uA Standby
* Fast Byte Write Time—200 us or 1 ms
* Data Retention >200 years
* High Endurance – Minimum 100,000 Erase/Write Cycles
* Automatic Write Operation
    * Internal Control Timer
    * Auto-Clear Before Write Operation
    * On-Chip Address and Data Latches
* Data Polling
* Ready/Busy
* Chip Clear Operation
* Enhanced Data Protection
    * VCC Detector
    * Pulse Filter
    * Write Inhibit
* Electronic Signature for Device Identificatio
* 5-Volt-Only Operation
* Organized 8Kx8 JEDEC Standard Pinout
    * 28-pin Dual-In-Line Package
    * 32-pin PLCC Package
    * 28-pin SOIC Package
* Available for Extended Temperature Ranges:
    * Commercial: 0°C to +70°C
    * Industrial:-40°C to +85°C

### Pinout diagram
The 28C64 Eeprom pinout is as follows:
```
             _________
RDY/nBSY [ 1|   \_/   |28] Vcc
     A12 [ 2|         |27] nWE
      A7 [ 3|         |26] NC
      A6 [ 4|         |25] A8
      A5 [ 5|    2    |24] A9
      A4 [ 6|    8    |23] A11
      A3 [ 7|    C    |22] nOE
      A2 [ 8|    6    |21] A10
      A1 [ 9|    4    |20] nCE
      A0 [10|    A    |19] I/O7
    I/O0 [11|         |18] I/O6
    I/O1 [12|         |17] I/O5
    i/O2 [13|         |16] I/O4
     Vss [14|_________|15] I/O3
```

### Making an adapter
An adapter for using the 28C64 as a replacement for 2532 has to be wired as follows:
```
           ____ _        28C64         _ ____
RDY/nBSY [|                              +-  |] Vcc
+--- A12 [|            _________         +-  |] nWE
|     A7 [|  ---  A7 [|   \_/   |] Vcc  -+-  |] NC
|     A6 [|  ---  A6 [|         |] A8   ---  |] A8
|     A5 [|  ---  A5 [|         |] A9   ---  |] A9
|     A4 [|  ---  A4 [|       +--------------|] A11
|     A3 [|  ---  A3 [|    2  | |] nPGM ---  |] nOE --+
|     A2 [|  ---  A2 [|    5  | |] A10  ---  |] A10   |
|     A1 [|  ---  A1 [|    3  +--- A11       |] nCE --+
|     A0 [|  ---  A0 [|    2    |] Q8   ---  |] I/O7
|   I/O0 [|  ---  Q1 [|         |] Q7   ---  |] I/O6
|   I/O1 [|  ---  Q2 [|         |] Q6   ---  |] I/O5
|   i/O2 [|  ---  Q3 [|         |] Q5   ---  |] I/O4
+--- Vss [|_ --- Vss [|_________|] Q4   --- _|] I/O3
```

Pin 1 of 28C64 (RDY/nBSY) and pin 21 of 2532 (VPP) are NOT connected! There is a total of 4 patches required. Connecting pin 2 (A12) of 28C64 to GND will limit it to lower 4kb.

## Downloads
* [2532 eprom datasheet (contains all 2532 information)](https://web.archive.org/web/20160424020222/https://mega.folderflex.com/websharedownload/MEGA/Adventure+Vision/file/575160)
* [25C64A eprom datasheet (contains all 28C64 information)](https://web.archive.org/web/20160424020222/https://mega.folderflex.com/websharedownload/MEGA/Adventure+Vision/file/575160)