# HVAC Control System - Technical Documentation

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Software Design](#software-design)
3. [Function Blocks](#function-blocks)
4. [Data Structures](#data-structures)
5. [Control Algorithms](#control-algorithms)
6. [HMI Design](#hmi-design)
7. [Code Reference](#code-reference)

---

## System Architecture

### Overview

The HVAC Control System is designed using a modular architecture with clear separation between:

- **Control Logic** (Function Blocks)
- **Data Management** (Global Variables)
- **User Interface** (Visualizations)
- **Simulation** (Virtual sensors and actuators)

### System Diagram

<img width="400" height="900" alt="1771069615201" src="https://github.com/user-attachments/assets/c38436c4-b6b6-4021-b2b7-a4fde9c53b47" />

### Component Hierarchy

```
PLC_PRG (Main Program)
├── Zone 1 Control
│   ├── FB_TempMonitor (Temperature Control)
│   ├── FB_TempSimulator (Temperature Simulation)
│   ├── FB_HumidityMonitor (Humidity Control)
│   ├── FB_HumiditySimulator (Humidity Simulation)
│   ├── FB_EnergyMonitor (Energy Tracking)
├── Zone 2 Control (same structure as Zone 1)
└── System-Wide Functions
    ├── Total Energy Calculation
    ├── Peak Power Tracking
    └── Mode Management
```

---

## Software Design

### Design Principles

1. **Modularity**: Each function block has a single, well-defined purpose
2. **Reusability**: Same function blocks used for both zones
3. **Separation of Concerns**: Control logic separate from simulation and UI
4. **Scalability**: Easy to add more zones by instantiating additional function blocks
5. **Maintainability**: Clear variable naming and comprehensive comments

### Programming Standards

**Language**: IEC 61131-3 Structured Text (ST)

**Naming Conventions**:

- Function Blocks: `FB_<Name>` (e.g., `FB_TempMonitor`)
- Data Structures: `ST_<Name>` (e.g., `ST_EnergyData`)
- Global Variable Lists: `GVL_<Category>` (e.g., `GVL_Sensors`)
- Variables:
  - Booleans: `b<Name>` (e.g., `bHeatingOutput`)
  - Integers: `i<Name>` (e.g., `iCurrentHour`)
  - Reals: `r<Name>` (e.g., `rTemperature`)
  - Timers: `ton<Name>` (e.g., `tonSampleTimer`)
  - Function Block instances: `fb<Name>` (e.g., `fbZone1Monitor`)

**Code Structure**:

```
// Variable declarations
VAR_INPUT
    // Input variables
END_VAR

VAR_OUTPUT
    // Output variables
END_VAR

VAR
    // Internal variables
END_VAR

// Implementation
// Clear, commented logic
```

---

## Function Blocks

### FB_TempMonitor

**Purpose**: Temperature control with heating and cooling capability

**Inputs**:

- `rActualTemp` (REAL): Current temperature measurement
- `rSetpoint` (REAL): Desired temperature
- `rHysteresis` (REAL): Dead-band to prevent rapid cycling

**Outputs**:

- `bHeatingCmd` (BOOL): Heating command
- `bCoolingCmd` (BOOL): Cooling command

**Logic**:

```
Heating Control:
  IF Temperature < (Setpoint - Hysteresis) THEN
    Heating = ON
  ELSIF Temperature > (Setpoint + Hysteresis) THEN
    Heating = OFF
  END_IF

Cooling Control:
  IF Temperature > (Setpoint + Hysteresis) THEN
    Cooling = ON
  ELSIF Temperature < (Setpoint - Hysteresis) THEN
    Cooling = OFF
  END_IF

Safety Interlock:
  IF Heating AND Cooling THEN
    Heating = OFF
    Cooling = OFF
  END_IF
```

**Control Band Diagram**:

![alt text](<Mermaid Chart - Create complex, visual diagrams with text.-2026-02-14-123837.png>)

### FB_HumidityMonitor

**Purpose**: Humidity control with dehumidification

**Inputs**:

- `rActualHumidity` (REAL): Current humidity measurement
- `rSetpoint` (REAL): Desired humidity
- `rHysteresis` (REAL): Dead-band (typically 3%)

**Outputs**:

- `bDehumidifyCmd` (BOOL): Dehumidifier command

**Logic**: Similar to temperature control but only dehumidification (no humidification in current implementation)

### FB_TempSimulator

**Purpose**: Simulate realistic temperature behavior

**Inputs**:

- `bHeatingOn` (BOOL): Heating active?
- `bCoolingOn` (BOOL): Cooling active?
- `rAmbientTemp` (REAL): Outside temperature
- `rHeatingRate` (REAL): Rate of temperature increase (°C/cycle)
- `rCoolingRate` (REAL): Rate of temperature decrease (°C/cycle)

**Outputs**:

- `rCurrentTemp` (REAL): Simulated temperature

**Physics Model**:

```
IF Heating Active:
  Temperature += HeatingRate
ELSIF Cooling Active:
  Temperature -= CoolingRate
ELSE:
  // Natural drift toward ambient
  IF Temperature > Ambient:
    Temperature -= CoolingRate
  ELSIF Temperature < Ambient:
    Temperature += HeatingRate
  END_IF
END_IF

// Apply realistic limits
Temperature = LIMIT(10.0, Temperature, 35.0)
```

### FB_HumiditySimulator

**Purpose**: Simulate humidity behavior

**Similar structure to FB_TempSimulator** with humidity-specific parameters

### FB_EnergyMonitor

**Purpose**: Track energy consumption and calculate costs

**Inputs**:

- `bHeating` (BOOL): Heating running?
- `bCooling` (BOOL): Cooling running?
- `bDehumidify` (BOOL): Dehumidifier running?
- `bHumidify` (BOOL): Humidifier running?
- `rCycleTime` (REAL): PLC cycle time in hours
- `rElectricityCost` (REAL): Cost per kWh

**In/Out**:

- `stEnergyData` (ST_EnergyData): Energy data structure (passed by reference)

**Calculations**:

```
Current Power (kW) = 
  (Heating ? HeatingPower : 0) +
  (Cooling ? CoolingPower : 0) +
  (Dehumidify ? DehumidifyPower : 0) +
  (Humidify ? HumidifyPower : 0)

Energy This Cycle (kWh) = Current Power × Cycle Time

Total Energy (kWh) += Energy This Cycle

```

---

## Data Structures

### ST_EnergyData

```
TYPE ST_EnergyData :
STRUCT
    rHeatingPower    : REAL := 2.5;    // Heating capacity (kW)
    rCoolingPower    : REAL := 2.0;    // Cooling capacity (kW)
    rDehumidifyPower : REAL := 0.5;    // Dehumidifier power (kW)
    rHumidifyPower   : REAL := 0.3;    // Humidifier power (kW)
    rTotalEnergy     : REAL := 0.0;    // Accumulated energy (kWh)
    rCurrentPower    : REAL := 0.0;    // Current power draw (kW)
END_STRUCT
END_TYPE
```

**Usage**: Stores all energy-related data for a zone or the total system

---

## Control Algorithms

### Hysteresis Control

**Purpose**: Prevent rapid ON/OFF cycling of equipment

**Implementation**:

```
Upper Threshold = Setpoint + Hysteresis
Lower Threshold = Setpoint - Hysteresis

Dead Band = [Lower Threshold, Upper Threshold]

Within Dead Band: Maintain current state
Above Upper Threshold: Turn equipment OFF
Below Lower Threshold: Turn equipment ON
```

**Benefits**:

- Reduces equipment wear
- Saves energy
- Provides stable control
- Prevents oscillation

**Example** (Temperature = 22°C, Hysteresis = 0.5°C):

- Heating turns ON at 21.5°C
- Heating turns OFF at 22.5°C
- Between 21.5-22.5°C: No state change

### Interlock Logic

**Purpose**: Prevent conflicting operations

**Implementation**:

```
Heating/Cooling Mutual Exclusion:
  IF bHeatingCmd AND bCoolingCmd THEN
    bHeatingCmd := FALSE;
    bCoolingCmd := FALSE;
  END_IF
```

**Safety Features**:

- Heating and cooling never operate simultaneously
- Protects equipment from damage
- Prevents energy waste
- Ensures system stability

### Energy Accumulation

**Purpose**: Track total energy consumption

**Method**: Numerical integration

```
Energy (kWh) = ∫ Power(t) dt

Discrete approximation:
Energy_new = Energy_old + (Power × ΔTime)

Where:
  Power = Current power draw (kW)
  ΔTime = PLC cycle time (hours)
```

**Implementation**:

```
rEnergyThisCycle := rCurrentPower * rCycleTime;
stEnergyData.rTotalEnergy := stEnergyData.rTotalEnergy + rEnergyThisCycle;
```

### Peak Demand Tracking

**Purpose**: Record maximum simultaneous power draw

**Implementation**:

```
IF stTotalEnergy.rCurrentPower > rPeakPower THEN
    rPeakPower := stTotalEnergy.rCurrentPower;
END_IF
```

**Application**:

- Utility demand charges
- Equipment sizing
- Load management

---

## HMI Design

### Visualization Architecture

**Screen Hierarchy**:

```
VIS_Dashboard (Main/Home)
  ├── VIS_Zone1 (Zone 1 Details)
  ├── VIS_Zone2 (Zone 2 Details)
  └── VIS_EnergyDashboard (Cumulative Stats)
```

### Design Principles

1. **Clarity**: Information is easy to read at a glance
2. **Consistency**: Similar elements look and behave the same way
3. **Feedback**: Visual indication of system state
4. **Efficiency**: Common tasks require minimal clicks
5. **Safety**: Critical actions require confirmation

### Color Coding Standards

**Status Indicators**:

- 🔴 **Red**: Heating active / High alarm
- 🔵 **Blue**: Cooling active / Low alarm
- 🟢 **Green**: Dehumidifying / Normal status
- 🟡 **Yellow**: Humidifying / Warning
- ⚫ **Gray**: Equipment OFF / Inactive

---

## Code Reference

### Main Program Structure (PLC_PRG)

```
PROGRAM PLC_PRG
VAR
    // Zone 1 instances
    fbZone1Monitor : FB_TempMonitor;
    fbZone1TempSim : FB_TempSimulator;
    // ... other Zone 1 function blocks
  
    // Zone 2 instances
    fbZone2Monitor : FB_TempMonitor;
    // ... other Zone 2 function blocks
  
    // System-wide variables
    rHeatingPower : REAL;
    rCoolingPower : REAL;
    // ...
END_VAR

// === ZONE 1 CONTROL ===
// Temperature simulation
fbZone1TempSim(...);

// Temperature control
fbZone1Monitor(...);

// Energy monitoring
fbZone1Energy(...);


// === ZONE 2 CONTROL === (similar structure)

// === SYSTEM-WIDE CALCULATIONS ===
// Total energy
// Peak power
// Mode management
```

### Global Variable Organization

**GVL_Sensors**: Sensor readings and actuator outputs

- Temperature actual values
- Temperature setpoints
- Humidity actual values
- Humidity setpoints
- Equipment outputs (heating, cooling, etc.)
- Mode indicators

**GVL_Energy**: Energy monitoring data

- Zone energy data structures
- Total system energy
- Peak power

### Execution Order

1. **Time simulation** (if using simulated time)
2. **Zone 1 temperature simulation**
3. **Zone 1 temperature control**
4. **Zone 1 humidity simulation**
5. **Zone 1 humidity control**
6. **Zone 1 energy monitoring**
7. **Zone 1 alarm detection**
8. **Zone 2** (same sequence as Zone 1)
9. **System-wide calculations** (totals, peak tracking)
10. **Mode management**

---

## Future Enhancements

### Planned Improvements

1. **PID Control**: Replace ON/OFF with PID for smoother control
2. **Advanced Scheduling**: Weekly/seasonal profiles
3. **Data Logging**: CSV export for analysis
4. **Predictive Algorithms**: Learn patterns and optimize
5. **Remote Access**: Web-based monitoring

### Scalability

The modular design allows easy expansion:

- Add zones by instantiating more function blocks
- Expand energy monitoring with additional equipment types
- Enhance alarm system with priority levels and logging
- Integrate with building management systems

---

## Appendix: Variable Reference

### GVL_Sensors Variables

| Variable                | Type | Description                |
| ----------------------- | ---- | -------------------------- |
| rZone1_TempActual       | REAL | Zone 1 current temperature |
| rZone1_TempSetpoint     | REAL | Zone 1 temperature target  |
| rZone1_HumidityActual   | REAL | Zone 1 current humidity    |
| rZone1_HumiditySetpoint | REAL | Zone 1 humidity target     |
| bZone1_HeatingOutput    | BOOL | Zone 1 heating command     |
| bZone1_CoolingOutput    | BOOL | Zone 1 cooling command     |
| bZone1_DehumidifyOutput | BOOL | Zone 1 dehumidify command  |
| bZone1_ComfortMode      | BOOL | Comfort mode active        |
| bZone1_EcoMode          | BOOL | Eco mode active            |
| bZone1_AwayMode         | BOOL | Away mode active           |
| bZone1_SleepMode        | BOOL | Sleep mode active          |

*(Zone 2 variables follow same pattern)*

### GVL_Energy Variables

| Variable      | Type          | Description         |
| ------------- | ------------- | ------------------- |
| stZone1Energy | ST_EnergyData | Zone 1 energy data  |
| stZone2Energy | ST_EnergyData | Zone 2 energy data  |
| stTotalEnergy | ST_EnergyData | System total energy |
| rPeakPower    | REAL          | Peak power demand   |

---

## Glossary

- **Hysteresis**: Dead band to prevent rapid cycling
- **Setpoint**: Target value for control
- **Interlock**: Safety logic preventing conflicting operations
- **kWh**: Kilowatt-hour, unit of energy
- **kW**: Kilowatt, unit of power
- **PLC**: Programmable Logic Controller
- **HMI**: Human-Machine Interface
- **SCADA**: Supervisory Control and Data Acquisition
- **IEC 61131-3**: International standard for PLC programming

---

*Technical Documentation v1.0 - Last Updated: February 2025*
