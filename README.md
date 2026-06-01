# Diseño de Sistemas Digitales: FSMD (Finite State Machine with Datapath)
## Arquitectura de Control y Ruta de Datos en Hardware (RTL)

Este repositorio está dedicado al diseño, simulación e implementación de **Máquinas de Estado Finito con Ruta de Datos (FSMD - Finite State Machine with Datapath)**. La arquitectura FSMD es el pilar fundamental del diseño a nivel de transferencia de registros (RTL), permitiendo la creación de procesadores, controladores de memoria, aceleradores de hardware y sistemas de procesamiento de señales en dispositivos como FPGA y ASIC.

La arquitectura se divide conceptualmente en dos bloques principales que trabajan de forma síncrona:
1.  **Unidad de Control (FSM - Controller)**: Una Máquina de Estados Finitos pura que gestiona el flujo de ejecución, toma decisiones basadas en señales de estado (*status*) provenientes de la ruta de datos, y genera señales de control (*control signals*) para dirigir las operaciones.
2.  **Ruta de Datos (Datapath)**: El bloque encargado de almacenar, procesar y manipular los datos (registros, sumadores, multiplicadores, multiplexores y comparadores), ejecutando operaciones aritmético-lógicas bajo la dirección de la unidad de control.

---

## 📋 Estructura Sugerida del Repositorio

Para organizar tus diseños en **Verilog** o **VHDL**, se recomienda estructurar el repositorio de la siguiente manera:

```
FSMD/
├── README.md               # Documentación técnica y académica principal
├── docs/
│   ├── diagramas_asm.md    # Cartas de Algoritmos de Máquinas de Estado (ASM Charts)
│   └── arquitectura_rtl.md # Especificación de la Unidad de Control y el Datapath
├── rtl/
│   ├── controller.v        # Unidad de Control (FSM) en Verilog / VHDL
│   ├── datapath.v          # Ruta de Datos (Registros, ALU, Muxes)
│   └── fsmd_top.v          # Módulo Top-Level que interconecta FSM y Datapath
├── tb/
│   └── fsmd_tb.v           # Banco de Pruebas (Testbench) para simulación síncrona
└── sim/
    └── wave.do             # Configuración de formas de onda (Waveforms) para simulación
```

---

## ⚙️ Principios de Diseño RTL (FSMD)

### 1. Separación de Control y Datos
Una regla de oro en el diseño digital es **nunca mezclar operaciones complejas de datos dentro de la lógica de estados de la FSM**. 
*   **Mal diseño**: Realizar sumas o multiplicaciones de registros de 32 bits dentro de un bloque `always @(posedge clk)` que gestiona los estados de la FSM. Esto genera un hardware ineficiente y rutas críticas de retardo.
*   **Buen diseño (FSMD)**: La FSM solo activa habilitadores (`enable`), señales de selección (`select`) o cargas de registros (`load`). El Datapath realiza la suma físicamente y le devuelve a la FSM señales como `zero_flag` o `overflow` para que tome decisiones en el siguiente ciclo de reloj.

### 2. Modelo de Comunicación Síncrona
```
              +-------------------+
              |  Entradas de      |
              |  Control Externas |
              +---------+---------+
                        |
                        v
              +---------+---------+
              |                   | <=== Señales de Status ===+
              | Unidad de Control |                           |
              |    (FSM Pure)     | ===> Señales de Control =+|
              |                   |                           |
              +---------+---------+                           |
                        |                                     |
                        v (Salidas de Control Externas)       |
                                                              |
              +---------+---------+                           |
              |                   | <=========================+
              |   Ruta de Datos   |
              |    (Datapath)     | <=== Entradas de Datos Externas
              |                   |
              +---------+---------+
                        |
                        v (Salidas de Datos Externas)
```

---

## 🚀 Guía de Simulación e Implementación

### Simulación Síncrona (ModelSim / QuestaSim / Vivado)
1.  **Compilación**: Compilar los archivos en orden jerárquico:
    ```bash
    vlog rtl/controller.v rtl/datapath.v rtl/fsmd_top.v tb/fsmd_tb.v
    ```
2.  **Inicialización de Simulación**: Cargar el testbench asegurando el clock y el reset síncrono:
    ```bash
    vsim work.fsmd_tb
    ```
3.  **Análisis de Formas de Onda**: Monitorear las señales críticas como `current_state`, `next_state`, los buses de datos internos, y las señales de control para verificar que la máquina transiciona correctamente y los datos se procesan sin retardos indeseados.

---
*Plantilla de diseño de hardware digital y arquitectura de computadores - 2026*
