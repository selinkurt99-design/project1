# Energy Management System Architecture

## Overview

This document presents a hierarchical control architecture for an energy management system with four layers: perception, high-level optimization, low-level actuation, and physical plant.

## System Architecture Diagram

```mermaid
graph TD
    %% Stil Tanımlamaları
    classDef layer fill:#f8fafc,stroke:#3b82f6,stroke-width:2px;
    classDef node fill:#ffffff,stroke:#475569,stroke-width:1px;
    
    %% 1. PERCEPTION LAYER
    subgraph L1 ["1. Perception Layer"]
        A["Driver Input"] --> B["Vehicle Dynamics"]
        B --> C{"P_dem (Disturbance)"}
    end

    %% 2. HIGH-LEVEL OPTIMIZATION
    subgraph L2 ["2. High-Level Optimization"]
        States[("States Feedback<br>SoC, SoC_sc, V1")]
        C --> D["Discrete-Time MPC"]
        States --> D
        D --> E1["I_batt_ref"]
        D --> E2["I_sc_ref"]
    end

    %% 3. LOW-LEVEL ACTUATION
    subgraph L3 ["3. Low-Level Actuation (20 kHz)"]
        E1 --> F1["Batt PI Controller"]
        E2 --> F2["SC PI Controller"]
        F1 --> G1["Batt DC/DC Converter"]
        F2 --> G2["SC DC/DC Converter"]
    end

    %% 4. PHYSICAL PLANT
    subgraph L4 ["4. Power Delivery & Plant"]
        G1 --> H{"500V DC Bus"}
        G2 --> H
        H --> I["Traction Motor Inverter"]
    end

    %% KAPALI DÖNGÜ GERİ BESLEMELERİ (Karmaşayı önlemek için dışarıdan dolanır)
    I -.->|Mechanical Torque| B
    G1 -.->|State Update| States
    G2 -.->|State Update| States

    %% Sınıfları Uygulama
    class L1,L2,L3,L4 layer;
    class A,B,C,D,States,E1,E2,F1,F2,G1,G2,H,I node;
```

## Architecture Layers

### Layer 1: Perception Layer
- **Driver Input**: Captures user commands and driving intentions
- **Vehicle Dynamics**: Models and processes vehicle kinematic/dynamic behavior
- **P_dem (Disturbance)**: Power demand signal derived from driving conditions

### Layer 2: High-Level Optimization
- **States Feedback**: Monitors system states including:
  - Battery State of Charge (SoC)
  - Supercapacitor State of Charge (SoC_sc)
  - Voltage (V1)
- **Discrete-Time MPC**: Model Predictive Controller that optimizes energy distribution
  - Outputs battery current reference (I_batt_ref)
  - Outputs supercapacitor current reference (I_sc_ref)

### Layer 3: Low-Level Actuation (20 kHz)
- **PI Controllers**: Regulate battery and supercapacitor currents to match references
  - Battery PI Controller → Battery DC/DC Converter
  - SC PI Controller → SC DC/DC Converter
- **High Frequency Loop**: Operates at 20 kHz for precise current control

### Layer 4: Power Delivery & Physical Plant
- **DC Bus**: Centralized 500V distribution point
- **Traction Motor Inverter**: Converts DC power to three-phase AC for motor drive
- **Mechanical Feedback**: Torque output feeds back to vehicle dynamics

## Feedback Loops

The system includes closed-loop feedback paths (shown as dashed lines):
- Mechanical torque from motor inverter → Vehicle dynamics
- State updates from converters → State feedback for MPC

## Control Flow Summary

1. **Perception** → Power demand is determined from driver input and vehicle state
2. **Optimization** → MPC decides optimal current references for battery and supercapacitor
3. **Actuation** → PI controllers execute the references through DC/DC converters
4. **Power Delivery** → Unified DC bus supplies the traction motor
5. **Feedback** → System states and mechanical outputs update perception and control layers
