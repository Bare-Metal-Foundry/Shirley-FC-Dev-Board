# JLink Cheatsheet ⛓️

Here we detail steps on how to use basic commands from the JLink Commander software.

> [!info] These are usefull steps to check if the MCU is working properly.
## Download and run

You can download `SEGGER J-Link Commander Vx.xx`from the web and then run in the terminal with:

```bash
JLinkExe
```

Note: Done on MacOS 
## Start a connection

Once JLink Commander launched you can connect to your MCU:

```
connect
```
 It will then ask for:
 - **Device**: STM32H743ZI
 - **Interface**: SWD
 - **Speed**: 1000 (for starting safe)

## Halt the CPU

You can halt the CPU :
```
halt
```

## Read registers

To read CPU registers
```
r
```

You’ll see values like:

```
R0 = 0x...PC = 0x...
```

## Read memory

### Flash:

```
mem32 0x08000000 4
```

### SRAM:

```
mem32 0x20000000 4
```

👉 If this works:

- Bus matrix OK
- Memory accessible
- Core not locked

## Erase flash

```
erase
```

👉 If it succeeds:

- Flash interface works
- Power is stable enough