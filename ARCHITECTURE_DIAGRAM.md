# Energy Management System Architecture

## System Overview

```mermaid
graph TD
    subgraph Perception["PERCEPTION LAYER"]
        A["Drive Cycle Profile<br/>WLTP / FTP-75"]
        B["Longitudinal Driver<br/>Model<br/>PI Velocity Tracker"]
        C["Longitudinal Vehicle<br/>Dynamics<br/>Plant"]
        A --> B --> C
    end
    
    subgraph Control["CONTROL LAYER"]
        D["Discrete-Time Model<br/>Predictive Control<br/>MPC"]
    end
    
    subgraph Power["POWER MANAGEMENT LAYER"]
        subgraph Battery["BATTERY CYCLE"]
            E1["PI Current<br/>Controller<br/>ph,ref"]
            F1["PWM Duty<br/>ph"]
            G1["Bidirectional<br/>Buck-Boost<br/>Converter"]
            H1["Lithium-ion<br/>Battery Pack"]
            E1 --> F1 --> G1 --> H1
        end
        
        subgraph Supercap["SUPERCAPACITOR CYCLE"]
            E2["PI Current<br/>Controller<br/>sc,ref"]
            F2["PWM Duty<br/>sc"]
            G2["Bidirectional<br/>Buck-Boost<br/>Converter"]
            H2["Supercapacitor"]
            E2 --> F2 --> G2 --> H2
        end
    end
    
    subgraph Plant["POWER OUTPUT"]
        I["DC Bus<br/>500V"]
        J["Traction Motor<br/>Inverter/PM Motor"]
    end
    
    C --> D
    D --> E1
    D --> E2
    H1 --> I
    H2 --> I
    I --> J
    J -.-> C
    H1 -.-> D
    H2 -.-> D
    
    style Perception fill:#c9d9e8
    style Control fill:#b3cce6
    style Power fill:#9db8e0
    style Battery fill:#d4e4f0
    style Supercap fill:#d4e4f0
    style Plant fill:#c9d9e8
```

## Architecture Layers

### 📋 Layer 1: Perception (Input)
- **Drive Cycle Profile**: Defines the driving scenario (WLTP, FTP-75)
- **Longitudinal Driver Model**: PI velocity tracker simulates driver behavior
- **Vehicle Dynamics Plant**: Models the vehicle's longitudinal motion

### 🎯 Layer 2: Control (Decision)
- **Discrete-Time MPC**: 
  - Receives vehicle velocity and power demand
  - Optimizes energy distribution between battery and supercapacitor
  - Outputs reference currents for both sources

### ⚡ Layer 3: Power Management (Execution)

#### Battery Cycle
1. PI Current Controller → Generates reference signal
2. PWM Duty → Controls switching frequency
3. Bidirectional Buck-Boost Converter → Regulates voltage/current
4. Lithium-ion Battery Pack → Primary energy source

#### Supercapacitor Cycle
1. PI Current Controller → Generates reference signal
2. PWM Duty → Controls switching frequency
3. Bidirectional Buck-Boost Converter → Regulates voltage/current
4. Supercapacitor → Auxiliary power source (fast transients)

### 🔋 Layer 4: Power Output
- **DC Bus (500V)**: Combines power from battery and supercapacitor
- **Traction Motor Inverter/PM Motor**: Converts electrical to mechanical power

## System Feedback

- **Dashed lines** represent feedback paths:
  - Motor output → Vehicle dynamics (mechanical coupling)
  - Battery state → MPC (energy management)
  - Supercapacitor state → MPC (power distribution)

## Key Features

| Component | Role | Function |
|-----------|------|----------|
| **MPC** | Control Strategy | Optimizes energy distribution |
| **PI Controllers** | Current Regulation | Maintains reference currents |
| **DC/DC Converters** | Power Conditioning | Bi-directional power flow |
| **DC Bus** | Power Distribution | 500V central distribution |
| **Motor Inverter** | Power Conversion | AC/DC conversion for motor |
