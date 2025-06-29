# LilyGo T-Relay S3 Irrigation Controller

A comprehensive ESPHome-based irrigation controller using the LilyGo T-Relay S3 board with simplified multiplexing for controlling up to 8 latching solenoid valves efficiently.

## Hardware Overview

- **Main Board**: LilyGo T-Relay S3 (ESP32-S3-WROOM-1U, 8 relays)
- **Chain Module**: T-Relay Chain module (PCF8574 I/O expander, 4 additional relays)
- **Total Relays**: 12 (8 main + 4 chain, uses 10 for 8 zones)
- **Supported Zones**: Up to 8 latching valve zones with simplified multiplexing
- **Efficiency**: Only 10 relays needed for 8 zones (vs 16 traditional)

## Features

- 🌱 **8 Zone Support**: Controls up to 8 irrigation zones with simplified multiplexing
- 🔄 **2-Wire Latching Valve**: Optimized for standard 2-wire latching solenoid valves
- 🛡️ **Maximum Safety**: Unselected zones completely isolated with no circuit path
- 🧠 **Simple Logic**: Intuitive relay control that's easy to understand and debug
- ⏰ **Flexible Scheduling**: 10-second default runtime for testing (configurable)
- 🎛️ **Dynamic Control**: Adjustable run times via Home Assistant
- 📱 **Web Interface**: Built-in web server for local control
- 🏠 **Home Assistant Integration**: Full ESPHome integration
- 🔧 **Sprinkler Controller**: Uses ESPHome's advanced sprinkler component

## Quick Start

### 1. Hardware Setup

#### Required Components

- 1x LilyGo T-Relay S3 board
- 1x LilyGo T-Relay Chain module
- 1x 24V DC power supply (adequate amperage for your valves)
- 8x Latching solenoid valves (or 7 latching + 1 regular)
- Appropriate wiring and connectors

#### Wiring Guide

See `wiring_instructions.md` for detailed step-by-step wiring instructions and `polarity_reversal_wiring_diagram.md` for system architecture.

**Key Connections:**

- **Simplified Multiplexing**: 2 control relays + 8 valve selectors
- **Black Wire Control**: All valve black wires to common bus
- **Red Wire Voltage Source**: Controls voltage available to valve red wires
- **I2C**: Chain module connects via GPIO21 (SDA) and GPIO22 (SCL)
- **Power**: 24V DC supply with proper current rating

### 2. Software Setup

#### Prerequisites

- [ESPHome](https://esphome.io/) installed
- Home Assistant (optional but recommended)
- USB-to-Serial adapter (T-U2T or similar)

#### Configuration Steps

1. **Clone/Download Files**

   ```bash
   git clone [your-repo]
   cd esphome-devices/lilygo/
   ```

2. **Setup Secrets**

   ```bash
   cp secrets.yaml.template secrets.yaml
   # Edit secrets.yaml with your WiFi credentials and API keys
   ```

3. **Compile and Upload**

   ```bash
   esphome compile irrigation_controller_simplified_multiplex.yml
   esphome upload irrigation_controller_simplified_multiplex.yml
   ```

4. **Initial Setup**
   - Connect to the device's fallback hotspot if WiFi setup fails
   - Configure via web interface at device IP address

## Configuration Details

### Zone Configuration

The controller supports 8 zones with simplified multiplexing using only 10 relays:

| Component               | Relay       | Function                        |
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

**Current Testing Configuration**: Only Zone 1 is configured for initial testing.

### Simplified Multiplexing Advantages

**Efficient Design**: The simplified multiplexing approach uses only 10 relays for 8 zones (vs 16 traditional).

**Key Benefits:**

1. **Relay Efficiency**

   - Uses 10 of 12 available relays (8 main + 2 chain)
   - 2 spare relays available for future expansion

2. **Simple Logic**

   - Black wire control: 0V for open, 24V+ for close
   - Red wire voltage source: provides appropriate voltage to red wires
   - Zone selectors: connect individual red wires to voltage source

3. **Maximum Safety**

   - Unselected zones have no complete circuit
   - No possibility of unintended valve activation

4. **Easy Expansion**
   - Add more zones by adding valve selector relays
   - No complex rewiring needed

### Default Irrigation Schedule

**Testing Configuration:**

- **Start Time**: 6:00 AM daily
- **Zone Duration**: 10 seconds per zone (for testing)
- **Inter-Zone Delay**: 1 minute between zones
- **Current Setup**: 1 zone configured for testing

**Full Production Configuration:**

- **Zone Duration**: 10 minutes per zone (configurable)
- **Total Cycle Time**: ~88 minutes (8 zones × 10 min + 7 delays × 1 min)

## Customization

### Changing Zone Names

Edit the `valve_switch` names in the sprinkler configuration:

```yaml
- valve_switch: "Your Custom Zone Name"
  enable_switch: "${friendly_name} Zone X Enable"
  run_duration: 10min
  valve_switch_id: zone_x_valve
```

### Adjusting Run Times

Default times can be changed in Home Assistant or by modifying the `initial_value` in the number components.

### Adding More Zones

To add zones 9-12 with a second chain module:

1. Add second PCF8574 configuration:

```yaml
pcf8574:
  - id: pcf8574_hub_2
    address: 0x21
    pcf8575: false
```

2. Add relay switches using the new hub
3. Create valve template switches
4. Add to sprinkler controller valves list

## Troubleshooting

### Common Issues

**Device Won't Connect to WiFi**

- Check credentials in `secrets.yaml`
- Connect to fallback hotspot: "irrigation-controller Fallback Hotspot"
- Password: "irrigation123" (unless changed)

**Relays Not Switching**

- Verify power supply voltage (24V DC)
- Check I2C connections (SDA/SCL)
- Confirm PCF8574 address (default 0x20)

**Valves Not Operating**

- Test individual relays via web interface
- Check valve wiring polarity
- Verify 24V power supply capacity
- Ensure common wire connections

**I2C Communication Issues**

- Check pullup resistors on SDA/SCL lines
- Verify chain module power
- Confirm I2C address conflicts

### Debug Mode

Enable verbose logging by adding to configuration:

```yaml
logger:
  level: DEBUG
```

## Advanced Features

### Manual Control

- Web interface available at device IP
- Individual zone control
- Real-time status monitoring

### Home Assistant Integration

- Automatic discovery with ESPHome
- Zone duration sliders
- Schedule automation
- Weather integration (add-on)

### Status Monitoring

- Active valve display
- Time remaining indicator
- WiFi signal strength
- Device uptime

## Safety Considerations

⚠️ **Important Safety Notes:**

- Use appropriate fuses on 24V supply lines
- Ensure weatherproof connections for outdoor use
- Proper grounding of ESP32 system required
- Test individual zones before full operation
- Consider valve manual overrides for emergencies

## Files in This Directory

- `irrigation_controller.yml` - Main ESPHome configuration
- `irrigation_wiring_diagram.md` - Detailed wiring schematic
- `secrets.yaml.template` - Configuration template
- `README.md` - This documentation

## References

- [ESPHome Sprinkler Controller](https://esphome.io/components/sprinkler.html)
- [LilyGo T-Relay Documentation](https://github.com/Xinyuan-LilyGO/LilyGo-T-Relay)
- [PCF8574 I/O Expander](https://esphome.io/components/pcf8574.html)

## License

This project is provided as-is for educational and personal use. Please ensure compliance with local electrical codes and irrigation regulations.

## Support

For issues and questions:

1. Check the troubleshooting section above
2. Review ESPHome documentation
3. Check LilyGo T-Relay GitHub issues
4. Community forums for ESPHome and Home Assistant
