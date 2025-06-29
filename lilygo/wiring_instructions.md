# Wiring Instructions - Simplified Multiplexing System

## Overview

This document provides **exact wiring instructions** for connecting the LilyGo T-Relay S3 to control latching solenoid valves using simplified multiplexing.

## System Components

- **LilyGo T-Relay S3** (8 main relays + 4 chain module relays)
- **24V Power Supply** (minimum 2A capacity)
- **Latching Solenoid Valves** (2-wire type: red and black)

## Relay Terminal Definitions

Each relay has 3 terminals:

- **COM** (Common): Always energized when relay is ON
- **NC** (Normally Closed): Connected to COM when relay is OFF
- **NO** (Nomally Open): Connected to COM when relay is ON

## Current Testing Configuration (1 Zone)

### Relay 1 - Black Wire Control (GPIO1)

**Function**: Controls voltage to all black valve wires

| Terminal | Connection            | Wire Gauge   | Description                          |
| -------- | --------------------- | ------------ | ------------------------------------ |
| **COM**  | All Black Valve Wires | 16 AWG Black | Common connection to all black wires |
| **NC**   | 0V (Power Supply -)   | 16 AWG Black | Provides 0V when relay OFF           |
| **NO**   | 24V+ (Power Supply +) | 16 AWG Red   | Provides 24V+ when relay ON          |

### Relay 2 - Red Wire Voltage Source (GPIO2)

**Function**: Controls voltage available to red wire selectors

| Terminal | Connection            | Wire Gauge   | Description                               |
| -------- | --------------------- | ------------ | ----------------------------------------- |
| **COM**  | All Valve Selector NO | 16 AWG Red   | Voltage source for all red wire selectors |
| **NC**   | 0V (Power Supply -)   | 16 AWG Black | Provides 0V when relay OFF                |
| **NO**   | 24V+ (Power Supply +) | 16 AWG Red   | Provides 24V+ when relay ON               |

### Relay 3 - Zone 1 Valve Selector (GPIO3)

**Function**: Connects Zone 1 red wire to voltage source

| Terminal | Connection                   | Wire Gauge | Description                  |
| -------- | ---------------------------- | ---------- | ---------------------------- |
| **COM**  | Zone 1 Valve Red Wire        | 18 AWG Red | Direct to Zone 1 red wire    |
| **NC**   | Not Connected                | -          | Leave unconnected            |
| **NO**   | Relay 2 COM (Voltage Source) | 16 AWG Red | Connection to voltage source |

## System Connections Overview

### Power Distribution

```
24V+ Power Supply ──┬── Relay 1 NO Terminal
                    └── Relay 2 NO Terminal

0V Power Supply ────┬── Relay 1 NC Terminal
                    └── Relay 2 NC Terminal
```

### Black Wire Distribution (Shared)

```
Relay 1 COM ──┬── Zone 1 Black Wire
              ├── Zone 2 Black Wire (future)
              ├── Zone 3 Black Wire (future)
              └── ... (all zone black wires in parallel)
```

### Red Wire Voltage Source Distribution

```
Relay 2 COM ──┬── Relay 3 NO (Zone 1 Selector)
              ├── Relay 4 NO (Zone 2 Selector - future)
              ├── Relay 5 NO (Zone 3 Selector - future)
              └── ... (all valve selector NO terminals)
```

### Individual Valve Connections

```
Zone 1 Valve:
├─ Red Wire ────── Relay 3 COM
└─ Black Wire ───── Relay 1 COM (shared with all zones)
```

## Operation States

### Opening Zone 1 Valve

1. **Black Wire Control**: Relay 1 OFF (NC) → All black wires = 0V
2. **Red Wire Source**: Relay 2 ON (NO) → Voltage source = 24V+
3. **Zone Selection**: Relay 3 ON (NO) → Zone 1 red wire = 24V+
4. **Result**: Red=24V+, Black=0V → **Valve opens**

### Closing Zone 1 Valve

1. **Black Wire Control**: Relay 1 ON (NO) → All black wires = 24V+
2. **Red Wire Source**: Relay 2 OFF (NC) → Voltage source = 0V
3. **Zone Selection**: Relay 3 ON (NO) → Zone 1 red wire = 0V
4. **Result**: Red=0V, Black=24V+ → **Valve closes**

### Null State (Safe)

- **Zone Selection**: Relay 3 OFF (NC) → Zone 1 red wire disconnected
- **Result**: No complete circuit → **No valve action**

## Wiring Sequence

### Step 1: Power Supply Connections

1. Connect 24V+ to Relay 1 NO terminal
2. Connect 24V+ to Relay 2 NO terminal
3. Connect 0V to Relay 1 NC terminal
4. Connect 0V to Relay 2 NC terminal
5. Verify power supply voltage (should be 24V DC)

### Step 2: Black Wire Common Connection

1. Install black wire bus (16 AWG black wire)
2. Connect black wire bus to Relay 1 COM terminal
3. Connect Zone 1 black valve wire to black wire bus
4. (Future zones: connect additional black wires to same bus)

### Step 3: Red Wire Voltage Source Distribution

1. Install red wire voltage bus (16 AWG red wire)
2. Connect red wire voltage bus to Relay 2 COM terminal
3. Connect Relay 3 NO terminal to red wire voltage bus
4. (Future zones: connect additional valve selector NO terminals to same bus)

### Step 4: Individual Valve Selector Connections

1. Connect Zone 1 red valve wire to Relay 3 COM terminal
2. Leave Relay 3 NC terminal unconnected
3. (Future zones: connect each zone's red wire to its own selector relay COM)

### Step 5: Verification

1. **Power off state**:
   - Measure Relay 1 COM to ground (should be 0V - from NC)
   - Measure Relay 2 COM to ground (should be 0V - from NC)
2. **Test Relay 1**: Turn ON → Relay 1 COM should read 24V+
3. **Test Relay 2**: Turn ON → Relay 2 COM should read 24V+
4. **Test Relay 3**: When ON, Zone 1 red wire should match Relay 2 COM voltage

## Safety Checks

### Before Power On

- [ ] All power connections secure and correct polarity
- [ ] No loose wires touching other terminals
- [ ] Black wire bus properly connected to all valve black wires
- [ ] Red wire voltage bus connected to all selector NO terminals
- [ ] Each valve red wire connected to its own selector COM

### After Power On

- [ ] System boots and connects to WiFi
- [ ] All relay LEDs respond to software commands
- [ ] Voltage measurements match expected values
- [ ] No overcurrent or short circuit conditions

### During Testing

- [ ] Only one valve selector active at a time
- [ ] Control relays set before activating valve selector
- [ ] Valve operates in correct direction (red+/black- = open)
- [ ] Unselected valves remain completely disconnected

## Troubleshooting

### No Valve Action

1. **Check power supply**: Verify 24V DC output
2. **Check control relays**: Verify Relay 1 & 2 respond to commands
3. **Check voltage at valve**: Measure red and black wire voltages during operation
4. **Check valve selector**: Verify Relay 3 connects red wire to voltage source

### Wrong Valve Direction

1. **Verify control relay states**: Relay 1 OFF + Relay 2 ON = open valve
2. **Check valve connections**: Red wire to selector COM, black wire to common bus
3. **Test opposite command**: Close should be Relay 1 ON + Relay 2 OFF

### Valve Selector Not Working

1. **Check voltage source**: Verify Relay 2 COM has correct voltage
2. **Check selector wiring**: NO terminal to voltage bus, COM to valve red wire
3. **Verify selector relay**: Should connect COM to NO when activated

## Expansion to Full 8-Zone System

### Additional Relays Needed

- **Relay 4-8**: Zone 2-6 selectors (GPIO4-8)
- **Chain Relay 1**: Zone 7 selector (Chain Pin 0)
- **Chain Relay 2**: Zone 8 selector (Chain Pin 1)

### Additional Wiring

1. **Extend black wire bus** to all additional valve black wires
2. **Extend red wire voltage bus** to all additional selector NO terminals
3. **Connect each valve red wire** to its individual selector COM terminal

### Relay Assignments (Full System)

| Component               | GPIO Pin    | Function                        |
| ----------------------- | ----------- | ------------------------------- |
| Black Wire Control      | GPIO1       | Control all black valve wires   |
| Red Wire Voltage Source | GPIO2       | Control red wire voltage source |
| Zone 1 Selector         | GPIO3       | Connect Zone 1 red wire         |
| Zone 2 Selector         | GPIO4       | Connect Zone 2 red wire         |
| Zone 3 Selector         | GPIO5       | Connect Zone 3 red wire         |
| Zone 4 Selector         | GPIO6       | Connect Zone 4 red wire         |
| Zone 5 Selector         | GPIO7       | Connect Zone 5 red wire         |
| Zone 6 Selector         | GPIO8       | Connect Zone 6 red wire         |
| Zone 7 Selector         | Chain Pin 0 | Connect Zone 7 red wire         |
| Zone 8 Selector         | Chain Pin 1 | Connect Zone 8 red wire         |

## Power Supply Considerations

- **Relay coils**: ~50mA each × 10 relays = 500mA
- **Valve operation**: ~500mA per valve × 1 active = 500mA
- **Total current**: ~1A (recommend 2A supply for safety margin)
- **Voltage**: 24V DC regulated supply

## Wire Specifications

| Connection Type              | Wire Gauge | Color     | Length   |
| ---------------------------- | ---------- | --------- | -------- |
| Power Supply Connections     | 16 AWG     | Red/Black | 2 feet   |
| Black Wire Bus               | 16 AWG     | Black     | 4 feet   |
| Red Wire Voltage Bus         | 16 AWG     | Red       | 4 feet   |
| Individual Valve Red Wires   | 18 AWG     | Red       | Variable |
| Individual Valve Black Wires | 18 AWG     | Black     | Variable |

## Key Advantages

✅ **Simple Logic**: Only 3 relay states to manage per valve operation  
✅ **Easy Wiring**: Clear bus connections, minimal complexity  
✅ **Complete Safety**: Unselected valves have no circuit path  
✅ **Easy Expansion**: Just add valve selectors, no rewiring needed  
✅ **Easy Troubleshooting**: Clear voltage paths, simple to debug

## Final Notes

- **Test thoroughly** with single zone before adding more zones
- **Label all connections** for easy maintenance
- **Document any modifications** to this wiring scheme
- **Take photos** of completed wiring for future reference
- **Keep wire diagrams** updated if layout changes

This simplified multiplexing provides **maximum reliability** with **minimum complexity**! 🔧⚡
