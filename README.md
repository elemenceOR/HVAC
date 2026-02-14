# Smart HVAC Control System

## CODESYS PLC Portfolio Project

![CODESYS](https://img.shields.io/badge/CODESYS-V3-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Project Overview

A comprehensive multi-zone HVAC (Heating, Ventilation, and Air Conditioning) control system developed in CODESYS. This project demonstrates professional PLC programming practices, including temperature and humidity control, energy monitoring, alarm management, and human-machine interface (HMI) design.

### Key Features

**Multi-Zone Control**

- Independent control of two zones (Living Room & Bedroom)
- Simultaneous heating, cooling, and dehumidification
- Customizable setpoints per zone

**Temperature & Humidity Management**

- Precision temperature control with hysteresis logic
- Humidity monitoring and dehumidification
- Automatic mode switching between heating and cooling

**Energy Monitoring**

- Real-time power consumption tracking
- Cumulative energy usage (kWh)
- Peak demand monitoring

**User Interface**

- Intuitive HMI with multiple visualization screens
- Pre-set comfort modes (Comfort, Eco, Away, Sleep)
- Manual setpoint adjustment with +/- controls
- Visual status indicators for all equipment

**Simulation Capability**

- Built-in temperature and humidity simulators
- Realistic thermal behavior modeling
- No hardware required for testing

---

## Project Purpose

This project was developed as a portfolio piece to demonstrate:

- Industrial automation programming skills
- PLC logic design and implementation
- HMI/SCADA development
- System integration and testing
- Professional documentation practices

---

## Technologies Used

- **Platform**: CODESYS V3.5
- **Programming Languages**:
  - Structured Text (ST) - Primary
  - IEC 61131-3 standards
- **Development Environment**: CODESYS Development System
- **Target**: CODESYS Control Win x64 (Soft PLC for simulation)
- **Visualization**: CODESYS TargetVisu

---

## Project Structure

```

HVAC_Control_System/
├── Application/
│   ├── PLC_PRG                    # Main program logic
│   ├── Function Blocks/
│   │   ├── FB_TempMonitor         # Temperature controller
│   │   ├── FB_TempSimulator       # Temperature simulation
│   │   ├── FB_HumidityMonitor     # Humidity controller
│   │   ├── FB_HumiditySimulator   # Humidity simulation
│   │   ├── FB_EnergyMonitor       # Energy tracking
│   ├── Data Types/
│   │   ├── ST_Schedule            # Scheduling structure
│   │   ├── ST_EnergyData          # Energy data structure
│   ├── Global Variables/
│   │   ├── GVL_Sensors            # Sensor and actuator variables
│   │   ├── GVL_Energy             # Energy monitoring variables
│   └── Visualizations/
│       ├── VIS_Dashboard          # Main overview screen
│       ├── VIS_Zone1              # Zone 1 detailed control
│       ├── VIS_Zone2              # Zone 2 detailed control
│       ├── VIS_EnergyDashboard    # Energy dashboard
└── Documentation/
    ├── README.md                  # This file
    ├── TECHNICAL_DOCUMENTATION.md # Technical details
```

---

## Quick Start

### Prerequisites

- CODESYS V3.5.x or later installed
- CODESYS Control Win x64 runtime (included with CODESYS)

### Running the Project

1. **Open the project**

   ```
   File → Open → Select HVAC_Control_System.project
   ```
2. **Build the application**

   ```
   Build → Build
   ```
3. **Start simulation**

   ```
   Online → Login
   Online → Start
   ```
4. **Open visualization**

   ```
   Double-click VIS_Dashboard in the project tree
   ```

---

## System Specifications

### Control Specifications

- **Temperature Range**: 15.0°C - 30.0°C
- **Temperature Hysteresis**: 0.5°C
- **Humidity Range**: 30% - 70%
- **Humidity Hysteresis**: 3.0%
- **Scan Cycle**: ~10ms (typical)

### Equipment Ratings

- **Heating Capacity**: 2.5 kW per zone
- **Cooling Capacity**: 2.0 kW per zone
- **Dehumidifier**: 0.5 kW per zone
- **Total System**: Up to 6.0 kW maximum

### Alarm Thresholds

- **Temperature High**: 28.0°C
- **Temperature Low**: 16.0°C
- **Humidity High**: 65.0%
- **Humidity Low**: 25.0%

---

## User Interface

### Pre-Set Modes

| Mode              | Temperature | Humidity | Use Case              |
| ----------------- | ----------- | -------- | --------------------- |
| **Comfort** | 22.0°C     | 50%      | Normal occupied hours |
| **Eco**     | 18.0°C     | 55%      | Energy saving mode    |
| **Away**    | 16.0°C     | 60%      | Extended absence      |
| **Sleep**   | 19.0°C     | 50%      | Nighttime comfort     |

### Controls

- **Temperature Adjustment**: ±0.5°C increments
- **Humidity Adjustment**: ±1.0% increments
- **Mode Selection**: One-click preset buttons
- **Manual Override**: Direct setpoint input

---

## Features Demonstrated

### PLC Programming Skills

- Function block design and reusability
- Structured programming with IEC 61131-3
- State machine implementation
- Timer and counter logic
- Data structure design
- Global variable management

### Control Algorithms

- ON/OFF control with hysteresis
- Interlock logic (heating/cooling mutual exclusion)
- Energy accumulation calculations
- Peak demand tracking

### HMI Development

- Multi-screen navigation
- Real-time data display
- Interactive controls
- Visual feedback (color-coded indicators)

---

## Future Enhancements

Potential improvements for future iterations:

- [ ] PID temperature control for better precision
- [ ] Advanced scheduling (weekly/seasonal profiles)
- [ ] Remote monitoring via web interface
- [ ] Data logging to CSV/database

---

## Documentation

- **[Technical Documentation](TECHNICAL_DOCUMENTATION.md)** - System architecture and code details

---

## Skills Demonstrated

This project showcases competency in:

- **Industrial Automation**: PLC programming, control logic, process automation
- **Software Engineering**: Modular design, code organization, documentation
- **Control Systems**: Feedback control, interlock logic, alarm management
- **HMI/SCADA**: User interface design, data visualization, operator interaction
- **Testing**: Simulation, validation, debugging
- **Documentation**: Technical writing, user guides, code comments

---

## License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## Acknowledgments

- Developed using CODESYS by 3S-Smart Software Solutions
- IEC 61131-3 programming standards
- Industrial automation best practices

---

## Version History

### Version 1.0.0 (Current)

- Multi-zone temperature and humidity control
- Energy monitoring
- Complete HMI with multiple screens
- Four pre-set comfort modes
- Manual setpoint adjustment
- Real-time power monitoring

---
