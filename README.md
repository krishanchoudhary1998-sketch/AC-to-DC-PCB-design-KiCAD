

# AC-DC Converter PCB Project

A KiCad-based PCB design for an AC-DC converter circuit. This project converts AC mains voltage to regulated DC output.


## Overview

This project implements a linear AC-DC converter circuit using a bridge rectifier topology with filtering and voltage regulation components. The PCB is designed for through-hole mounting components, making it suitable for DIY electronics and educational purposes.

## Hardware Specifications

### Input Specifications
- **Input Voltage:** 110V-240V AC (mains voltage)
- **Input Frequency:** 50Hz/60Hz
- **Input Connector:** Screw Terminal 2-pin (5mm pitch)

### Output Specifications
- **Output Voltage:** Dependent on transformer/regulator configuration
- **Output Connector:** Screw Terminal 2-pin (5mm pitch)
- **Current Rating:** Up to 500mA (limited by 1N4004 diodes)
- **Ripple Voltage:** <10% (with 1000uF filter capacitor)

### Electrical Characteristics

| Parameter | Value | Notes |
|-----------|-------|-------|
| Input Voltage Range | 110V - 240V AC | Universal input |
| Output Current (max) | 500mA | Continuous load |
| Peak Inverse Voltage (PIV) | 400V | Per diode (1N4004) |
| Forward Current (If) | 1A | Per diode |
| Filter Capacitor | 1000uF | 25V rating minimum |
| LED Forward Current | 20mA | Max continuous |
| Power Dissipation | ~2W | At full load |

### Power Calculations

**Rectifier Output (no load):**
```
Vdc = Vrms × √2 - 2 × Vd
Vdc = 230V × 1.414 - 2 × 0.7V
Vdc ≈ 324V DC (at 230V input)
```

**With Transformer (assuming 12V AC secondary):**
```
Vdc = 12V × 1.414 - 2 × 0.7V
Vdc ≈ 15.7V DC (unloaded)
```

**Current Limiting Resistor (LED):**
```
Rled = (Vout - Vled) / Iled
Rled = (15.7V - 2V) / 0.02A
Rled ≈ 685Ω → Use 2.2K for safety
```

### Components Used

| Reference | Component | Value | Package | Datasheet |
|-----------|-----------|-------|---------|-----------|
| J1 | Input Terminal | 2-pin | MaiXu MX126-5.0-02P | [MX126 Datasheet](https://www.lcsc.com/datasheet/lcsc_datasheet_2309150913_MAX-MX126-5-0-03P-GN01-Cu-S-A_C5188435.pdf) |
| J2 | Output Terminal | 2-pin | MaiXu MX126-5.0-02P | [MX126 Datasheet](https://www.lcsc.com/datasheet/lcsc_datasheet_2309150913_MAX-MX126-5-0-03P-GN01-Cu-S-A_C5188435.pdf) |
| D1-D4 | Rectifier Diode | 1N4004 | DO-41 | [1N4004 Datasheet](https://www.diodes.com/assets/Datasheets/1N4000.pdf) |
| C1 | Filter Capacitor | 1000uF 25V | Electrolytic | Generic |
| R1 | Bleeder Resistor | 10K 1/4W | Carbon Film | Generic |
| R2 | LED Resistor | 2.2K 1/4W | Carbon Film | Generic |
| D5 | Power LED | Red 5mm | 5mm LED | Generic |

### Component Sourcing

| Part | Suggested Supplier | Part Number |
|------|-------------------|-------------|
| 1N4004 Diodes | DigiKey, Mouser, LCSC | 1N4004-TP |
| 1000uF Capacitor | DigiKey, Mouser | 25PX1000MEFCT |
| 10K Resistor | DigiKey, Mouser | CF14JT10K0CT |
| 2.2K Resistor | DigiKey, Mouser | CF14JT2K20CT |
| Red LED | DigiKey, Mouser | LTL-1CHGE-012 |
| Screw Terminals | DigiKey, Mouser | MX126-5.0-02P |

## Circuit Theory

### Full-Wave Bridge Rectifier

The bridge rectifier uses four diodes arranged in a diamond configuration to convert AC to DC:

```
      D1 (↑)      D2 (↓)
       ↑          ↓
    ───●─── AC ───●───
       ↓          ↑
      D4 (↓)     D3 (↑)
```

**How it works:**
1. During positive half-cycle: Current flows through D1 → load → D3
2. During negative half-cycle: Current flows through D2 → load → D4
3. Both half-cycles produce current in the same direction through the load

**Advantages:**
- Utilizes both halves of the AC waveform
- No center-tapped transformer required
- Higher efficiency than half-wave rectifier

**Disadvantages:**
- Two diode drops in series (1.4V total)
- More complex wiring than center-tapped design

### Filter Capacitor

The 1000uF electrolytic capacitor acts as a reservoir:

**Ripple Voltage Calculation:**
```
Vr = Iload / (f × C)
Vr = 0.5A / (100Hz × 0.001F)
Vr = 5V p-p (theoretical minimum)
```

With actual ESR and load variations, expect ~5-10% ripple at full load.

### Bleeder Resistor (R1)

The 10K resistor serves multiple purposes:
- Discharges filter capacitor when power is removed
- Provides minimal load for stability
- Power dissipation: V²/R = 15.7²/10000 ≈ 25mW

## PCB Layout Details

### Design Rules

| Parameter | Value |
|-----------|-------|
| Minimum Trace Width (Signal) | 0.25mm (10mil) |
| Minimum Trace Width (Power) | 0.5mm (20mil) |
| Minimum Clearance | 0.25mm (10mil) |
| Minimum Via Size | 0.6mm/0.3mm (drill/pad) |
| Copper Thickness | 35µm (1oz) |
| Board Thickness | 1.6mm |

### PCB Stack-up

```
Top Layer (Copper)     : 35µm copper
Prepreg                : FR-4 1.6mm
Bottom Layer (Copper)  : 35µm copper
Soldermask             : Green (both sides)
Silkscreen             : White (top only)
```

### Trace Width Calculations

**High Voltage Traces (AC Input):**
- Current: 50mA (assuming 5W load at 230V)
- Temperature rise: 10°C
- Required width: 0.3mm minimum

**DC Output Traces:**
- Current: 500mA max
- Temperature rise: 10°C
- Required width: 0.8mm minimum

**LED/Indicator Traces:**
- Current: 20mA
- Required width: 0.25mm minimum

### Component Placement

```
    ┌─────────────────────────────────────────┐
    │                                         │
    │  J1 (AC IN)                             │
    │   ○  ○                                  │
    │   │  │                                  │
    │  D1 D2                                  │
    │   ○──○                                  │
    │     │                                   │
    │   ═══════ C1 (1000uF)                   │
    │     │                                   │
    │  D3 D4                                  │
    │   ○──○                                  │
    │     │                                   │
    │   ○  ○  J2 (DC OUT)                     │
    │                                         │
    │            R1 (10K)  D5 LED             │
    │              ○──○──○──○                 │
    │                                 R2      │
    │                                 (2.2K) │
    └─────────────────────────────────────────┘
```

### Safety Clearances

- **Mains Input to Low Voltage:** 6mm minimum (reinforced isolation)
- **Mains traces to board edge:** 3mm minimum
- **Between high-voltage nodes:** 2mm minimum

## Project Structure

```
AC-DC converter/
├── AC-DC converter.kicad_pcb      # PCB layout file
├── AC-DC converter.kicad_sch      # Schematic file
├── AC-DC converter.kicad_pro      # KiCad project file
├── AC-DC converter.kicad_prl      # Project preferences
├── AC-DC converter-backups/       # Auto-saved backups
├── AC-DC converter-images/        # Schematics & 3D renders
└── README.md                      # This file
```

## Visual Documentation

### How to Generate Images

To generate the schematic and 3D render images:

**Schematic Image:**
<img width="646" height="560" alt="Screenshot 2026-05-17 at 3 04 52 AM" src="https://github.com/user-attachments/assets/430a467f-1cb9-4b91-aaec-01472d0a5f57" />


**3D Render Images:**
<img width="792" height="594" alt="Screenshot 2026-05-17 at 3 05 08 AM" src="https://github.com/user-attachments/assets/5927f8b4-1213-429c-9629-340427abe61f" />



## Getting Started

### Requirements
- KiCad 10.0 or later
- Computer with Windows/macOS/Linux

### Opening the Project
1. Download and install KiCad from [kicad.org](https://kicad.org)
2. Open KiCad
3. Go to File > Open > Project
4. Navigate to `AC-DC converter.kicad_pro` and open it

### Viewing Schematics
1. Open the `.kicad_sch` file in KiCad's Eeschema
2. Use the toolbar to navigate and inspect the circuit
3. Press F8 to annotate or run ERC (Electrical Rule Check)

### Viewing PCB
1. Open the `.kicad_pcb` file in KiCad's PCBNew
2. Use the 3D viewer (Alt+3) to see a 3D render of the board
3. Use the Ratsnest tool to verify all connections

## Manufacturing

### Generating Gerbers
1. Open PCBNew
2. Go to File > Plot
3. Select output directory (create a "gerber" folder)
4. Choose Gerber format
5. Select these layers:
   - F.Cu (Front Copper)
   - B.Cu (Back Copper)
   - F.Silkscreen (Top Silkscreen)
   - B.Silkscreen (Bottom Silkscreen)
   - F.Mask (Top Soldermask)
   - B.Mask (Bottom Soldermask)
   - Edge.Cuts (Board Outline)
   - Drills (Excellon format)
6. Click "Plot"

### Recommended Manufacturing Settings
- **Manufacturer:** JLCPCB, PCBWay, Seeed Studio
- **Layers:** 2 layers
- **Board Thickness:** 1.6mm (standard)
- **Copper Weight:** 1oz (35µm)
- **Color:** Green (standard)
- **Surface Finish:** HASL (Lead-free)
- **Minimum Trace/Space:** 6/6mil (0.15mm)


## License

This project is provided as-is for educational and personal use.

## Version History

- **v1.0** - Initial design with bridge rectifier and filtering

---

*Project created with KiCad Eeschema 10.0*
*Last updated: May 2026*
