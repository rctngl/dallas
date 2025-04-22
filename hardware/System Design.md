# System Design

## Core Specifications

- **Input Voltage:** 12-20V
- **Output Current:** Up to 20A
- **Output Volttage:** Regulated via Current Loop (Load Dependent)
- **Current setpoint:** Controlled by ESP32

## Core Design

A majority of this design is based on the LM5145EVM-HD-20A [Evaluation Board](https://www.ti.com/tool/LM5145EVM-HD-20A) but with the following core changes:
- Single sided PCB with less space constraints
- Wider output voltage (~20V instead of 5V)
- MCU Controlled current limit

## Bill of Materials

### Control and Feedback

Ref | Part | Value | Mouser | Datasheet
|--|---|---|---|---|
| U1 | LM5145RGYR | Sync Buck Controller | [595-LM5145RGYR](https://au.mouser.com/ProductDetail/Texas-Instruments/LM5145RGYR?qs=gt1LBUVyoHkKVoSLabi7ZQ%3D%3D) | [Link](https://www.ti.com/lit/gpn/lm5145)

### Power Stage

Ref | Part | Value | Mouser | Datasheet
|--|---|---|---|---|
| Q1 | BSC117N08NS5ATMA1 | 80V **49A** N-CH | [726-BSC117N08NS5ATMA](https://au.mouser.com/ProductDetail/Infineon-Technologies/BSC117N08NS5ATMA1) | [Link](https://au.mouser.com/datasheet/2/196/Infineon_BSC117N08NS5_DataSheet_v02_01_EN-3360867.pdf)
| Q2 | BSC037N08NS5ATMA1 | 80V **100A** N-CH | [726-BSC037N08NS5ATMA](https://au.mouser.com/ProductDetail/Infineon-Technologies/BSC037N08NS5ATMA1) | [Link](https://au.mouser.com/datasheet/2/196/Infineon_BSC037N08NS5_DataSheet_v02_03_EN-3360724.pdf)
| L1 | SER2918H-103KL | 10uH 28A 2.86mOhm | [994-SER2918H-103KL](https://au.mouser.com/ProductDetail/Coilcraft/SER2918H-103KL) | [Link](https://au.mouser.com/datasheet/2/597/ser2900-270685.pdf)

#### Q1 and Q2 Mosfets
    These are the mosfets used in the Reference Design

In high current bucks, Q2 sees more abuse as Q1 only conducts during **ON Time**. Q2 conducts the full load when Q1 is off and is the current sink for the inductor. In case of a measurement overshoot and more working head room, a Q2 with more rated current is used.

Careful consideration for temp management as these FETS are at their temp limits when running 20A. 2oz copper and 4 layers should be used to manage heat. Potentially a fan?


#### L1 - Inductor
Reference design uses a 3.3uH inductor with an acceptable 22A Current and 40A Saturation current but would create apporx 1.5A current ripple. Need to lower this ripple as much as possible to clean up the `Rsense` voltage ripple into the OpAmp for tighter current loop control.

Inductor of at least 10uH required with 20A Constant and 30A Saturation Current Specs. 