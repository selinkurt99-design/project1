# Energy Management System Architecture

## System Overview

```mermaid
graph TD
    A["Driver Input"] --> B["Vehicle Dynamics"]
    B --> C["Power Demand<br/>P_dem"]
    
    C --> D["MPC<br/>Controller"]
    E["System State<br/>SoC, Voltage"] --> D
    
    D --> F["Battery<br/>Current Ref"]
    D --> G["Supercap<br/>Current Ref"]
    
    F --> H["Battery<br/>Controller"]
    G --> I["SC<br/>Controller"]
    
    H --> J["Battery<br/>Converter"]
    I --> K["SC<br/>Converter"]
    
    J --> L["500V DC Bus"]
    K --> L
    
    L --> M["Motor Inverter"]
    M --> N["Mechanical Power"]
    
    N -.-> B
    J -.-> E
    K -.-> E
    
    style A fill:#e3f2fd
    style B fill:#e3f2fd
    style C fill:#fff3e0
    style D fill:#f3e5f5
    style E fill:#f3e5f5
    style F fill:#e8f5e9
    style G fill:#e8f5e9
    style H fill:#e8f5e9
    style I fill:#e8f5e9
    style J fill:#e8f5e9
    style K fill:#e8f5e9
    style L fill:#fce4ec
    style M fill:#fce4ec
    style N fill:#fce4ec
```

## Architecture Layers

### 📊 Layer 1: Perception (Input)
- Receive driver commands
- Calculate vehicle dynamics
- Determine power demand

### 🎯 Layer 2: Decision (Optimization)
- **MPC Controller**: Decides optimal energy distribution between battery and supercapacitor
- Monitors system state (SoC, voltage)
- Calculates reference current for each source

### ⚡ Layer 3: Control (Execution)
- PI controllers track reference currents
- DC/DC converters transform power
- Operates at 20 kHz (fast response)

### 🔋 Layer 4: Power (Physical)
- Battery and supercapacitor combine
- 500V DC bus delivers power to motor inverter
- Motor converts electrical to mechanical power

## Operation Flow

1. **Input** → Driver presses pedal, power demand is determined
2. **Decision** → MPC calculates optimal distribution
3. **Control** → Controllers adjust currents to targets
4. **Output** → Motor produces mechanical power
5. **Feedback** → System state updates (dashed lines)

## Key Features

| Feature | Description |
|---------|-------------|
| **Power Sources** | Battery + Supercapacitor |
| **Power Bus** | 500V DC |
| **Control Frequency** | 20 kHz |
| **Control Method** | Model Predictive Control (MPC) |
| **Feedback** | SoC, Voltage, Torque |
