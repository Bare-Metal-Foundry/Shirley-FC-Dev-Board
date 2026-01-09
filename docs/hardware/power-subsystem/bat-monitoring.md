# Voltage & Current Measurement (Pixhawk Power Connector → STM32H7)

## 1. Overview

The Pixhawk-style **POWER** connector exposes two **analog** signals:

- **VOLTAGE**: battery voltage via a resistor divider (max ≈ 3.3 V)
- **CURRENT**: load current via a current-sense circuit (max ≈ 3.3 V)

These are read by the STM32H7’s ADC as **single-ended analog inputs**.

> ⚠ The exact scaling (divider ratio & A/V gain) depends on the power module.
> Check its datasheet for numerical values.

---

## 2. Hardware Connections

Assuming use of **ADC1 Channel 2 & 3** (example):

- `POWER.VOLTAGE` → MCU pin mapped to **ADC1_IN2** (Rank 1)
- `POWER.CURRENT` → MCU pin mapped to **ADC1_IN3** (Rank 2)
- GND of the power module tied to MCU analog GND
- Optional small RC filters at the MCU pins (e.g. 1–4.7 kΩ + 100 nF to GND)

All signals stay within **0 – 3.3 V** (ADC reference range).

---

## 3. STM32CubeIDE ADC Configuration (ADC1)

**Global settings**

- Resolution: **12 bits**
- Scan Conversion Mode: **Enabled**
- Continuous Conversion Mode: **Enabled**
- Discontinuous Mode: **Disabled**
- End of Conversion Selection: **End of single conversion**
- Overrun behaviour: **Overrun data preserved**
- Conversion Data Management: **Regular Conversion data stored in DR**

**Regular Conversion Mode**

- Enable Regular Conversions: **Enabled**
- Number Of Conversions: **2**
- External Trigger Source: **Regular Conversion launched by software**
- External Trigger Edge: **None**

**Ranks**

- Rank 1  
  - Channel: **Channel 2** (ADC1_IN2 → VOLTAGE)  
  - Sampling Time: **64.5 Cycles**  
  - Offset: **None**
- Rank 2  
  - Channel: **Channel 3** (ADC1_IN3 → CURRENT)  
  - Sampling Time: **64.5 Cycles**  
  - Offset: **None**

(Other channels: **Disabled**, Single-ended mode.)


