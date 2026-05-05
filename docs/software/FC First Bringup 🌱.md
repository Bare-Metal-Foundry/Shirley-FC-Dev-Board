# FC First Bringup 🌱

Here are the steps done to progressively bringup and check the features and components of the Shirley FC:

1. [[#Power stages check]]
2. [[#STM32 power up]]
3. [[#Debug interface (SWD)]]
4. [[#Flash blink program]]

## Power stages check

Check that all voltages are nominal before soldering the jumper resistors that connects the other components

## STM32 power up

Solder the required components for a first STM32 bringup:
- jumper resistors
- BOOT0 slide switch

Check of the voltages are still nominal and stable

## Debug interface (SWD)

- Connect the debug interface (JLink) to the board
- Power the board
- connect the interface to the host PC

Thanks to the debug interface we can then check if the MCU is alive and his basic features work properly. See [Guide on the JLink instructions]([Doc/JLink Cheatsheet ⛓️](obsidian://open?vault=FC-soft-toolkit&file=Doc%2FJLink%20Cheatsheet%20%E2%9B%93%EF%B8%8F))

## Flash blink program 

The next step is to check if we can flash a basic program onto the STM32. We use STM32CubreIDE/STM32CuberMX as it is the easiest/most reliable way to do so.

We configure the LedSys pin output on CubeMX and write a simple led blinking program:
`STM32CubeIDE/LedBlinkTest`
### 1. Internal clock (HSI)

We configure the clock with the HSI at 64Mhz and the prescalers set to 1.
[See STM32 Clock settings](Doc/STM32 Settings ⚙️)
### 2. External Crystal (HSE)

We enable HSE (16MHz) and put it trough PLLCLK to get 80MHz as the CPU clock. 
This allows us to check the External crystal is working properly.

