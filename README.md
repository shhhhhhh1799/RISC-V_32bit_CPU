# ⚙️ RISC-V 32bit CPU Design

본 프로젝트는 **RISC-V ISA (RV32I)** 기반의 **32bit 멀티 사이클 CPU**를 설계하고,  
**SystemVerilog**로 구현하여 **시뮬레이션 검증**을 진행한 프로젝트입니다.  

멀티 사이클 구조를 채택하여, 명령어가 **여러 클록 사이클(IF → ID → EX → MEM → WB)** 동안 수행되며,  
Control Unit은 FSM 기반으로 상태를 전환하며 각 단계의 동작을 제어합니다.  

---

## 🧑‍💻 프로젝트 개요

| 항목          | 내용                                          |
|---------------|-----------------------------------------------|
| **진행 기간** | 25.08.12 ~ 25.08.24                           |
| **개발 환경** | Vivado, VSCode, Verdi, VCS                    |
| **언어**      | SystemVerilog                                 |
| **구조**      | RV32I 명령어셋 기반 32bit **멀티 사이클 CPU**  |
| **검증 방식** | Testbench 및 시뮬레이션 (R/I/S/B/L/J-Type 명령어 실행 확인) |

---

## 📁 주요 모듈 설명

| 모듈               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 📜 `Instruction Memory` | ROM에 명령어 저장, PC에 따라 Fetch |
| 📒 `Register File` | 32×32bit 레지스터, RA1/RA2 읽기 및 WA 쓰기 |
| ➗ `ALU`           | ADD, SUB, AND, OR, SLL, SRL, SRA, SLT/SLTU 수행 |
| 🕹 `Control Unit`   | 상태기계 기반으로 IF → ID → EX → MEM → WB 단계 제어 |
| 💾 `Data Memory`   | Load/Store (LW, LH, LB, SW, SH, SB) 명령어 실행 |
| 📐 `PC (Program Counter)` | 명령어 주소 관리 (분기/점프 지원) |

---

### 🧩 CPU Block Diagram
아래 그림은 **CPU 전체 Block Diagram**입니다.  
Instruction Memory(ROM), Data Memory(RAM), Control Unit, Datapath가 어떻게 연결되는지 한눈에 볼 수 있습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/Image/blob/main/Block%20Diagram.png" alt="CPU Block Diagram" width="900"/>
</p>
---

### 🧩 CPU Top Schematic
아래 그림은 Vivado에서 생성한 **CPU Top-Level RTL Schematic**입니다.  
Instruction Memory, Register File, ALU, Data Memory, Control Unit 등이 연결된 전체 구조를 보여줍니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/Image/blob/main/CPU-Top.png" alt="CPU Block Diagram" width="900"/>
</p>

---

### 🧩 CPU RV32I 내부 구조
아래 그림은 **CPU_RV32I 모듈 내부 구조**를 보여줍니다.  
Control Unit과 Datapath가 어떻게 상호 연결되어 제어 신호와 데이터 신호를 주고받는지 확인할 수 있습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/Image/blob/main/CPU-RV32I.png" alt="CPU RV32I Internal Structure" width="900"/>
</p>

---

### 🧩 CPU Datapath
아래 그림은 **Datapath 모듈 내부 구조**를 보여줍니다.  
Register File, ALU, Immediate Generator, Program Counter(PC), MUX 등이 서로 연결되어 명령어 실행을 수행하는 과정을 표현합니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/Image/blob/main/CPU-DataPath.png" alt="CPU Datapath" width="900"/>
</p>

---

## 🧩 Multi-Cycle FSM 구조

멀티 사이클 CPU는 **Control Unit**의 FSM에 의해 동작하며, 각 상태에서 필요한 하드웨어 블록만 활성화됩니다.

- **IF (Instruction Fetch)** : PC → Instruction Memory에서 명령어 읽기  
- **ID (Instruction Decode)** : Opcode 해석, Register File 읽기, Immediate 생성  
- **EX (Execute)** : ALU 연산 수행 (산술/논리/분기)  
- **MEM (Memory Access)** : Data Memory 읽기/쓰기  
- **WB (Writeback)** : 결과를 Register File에 저장  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/Multi-Cycle%20FSM.png" alt="Multi Cycle FSM" width="700"/>
</p>

---

### 🔹 Multi-Cycle FSM 시뮬레이션 검증
아래는 각 명령어 유형별 FSM 상태 전이를 시뮬레이션으로 확인한 결과입니다.  
- **I-Type** : IF → ID → EX → FETCH  
- **S-Type** : IF → ID → EX → MEM → FETCH  
- **L-Type** : IF → ID → EX → MEM → WB → FETCH  

이를 통해 Multi-Cycle 구조에서 명령어별로 수행 단계가 달라지는 것을 검증할 수 있습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/FSM.png" alt="Multi-Cycle FSM Simulation" width="900"/>
</p>

---


## 🧪 Type별 검증

---

### 🔹 R-Type 명령어 검증
아래는 `R-Type` 명령어(`ADD`, `SUB`, `AND`, `OR`, `XOR`, `SLL`, `SRL`, `SRA`, `SLT`, `SLTU`) 실행을 검증한 결과입니다.  
테스트 벡터에 따라 연산 결과가 올바르게 Register File에 기록되는 것을 확인할 수 있습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/R-Type.png" alt="R-Type Verification" width="900"/>
</p>

---

### 🔹 I-Type 명령어 검증
아래는 `I-Type` 명령어(`ADDI`, `SLTI`, `SLTIU`, `XORI`, `ORI`, `ANDI`, `SLLI`, `SRLI`, `SRAI`) 실행을 검증한 결과입니다.  
즉시값(imm) 연산이 정상적으로 수행되는지 확인할 수 있으며, Register File에 연산 결과가 반영됩니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/I-Type.png" alt="I-Type Verification" width="900"/>
</p>

---

### 🔹 S-Type 명령어 검증
아래는 `S-Type` 명령어(`SW`, `SH`, `SB`) 실행을 검증한 결과입니다.  
메모리에 데이터가 올바르게 저장되는지 확인할 수 있으며, 파형에서 Write 동작이 정상적으로 수행됨을 보여줍니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/S-Type.png" alt="S-Type Verification" width="900"/>
</p>

---

### 🔹 R-Type 명령어 검증
아래는 `R-Type` 명령어 실행 결과를 자세히 검증한 파형과 결과 테이블입니다.  
모든 결과가 예상 값과 일치하며, Register File에 올바르게 반영됨을 확인했습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/R-Type.png" alt="R-Type Verification" width="900"/>
</p>

---

### 🔹 B-Type 명령어 검증
아래는 `B-Type` 명령어(`BEQ`, `BNE`, `BLT`, `BGE`, `BLTU`, `BGEU`) 실행을 검증한 결과입니다.  
분기 조건에 따라 Program Counter(PC)가 올바르게 갱신되는지 확인할 수 있으며, 파형에서 조건이 True/False로 판별되는 과정을 보여줍니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/B-Type.png" alt="B-Type Verification" width="900"/>
</p>

---

### 🔹 U-Type 명령어 검증
아래는 `U-Type` 명령어(`LUI`, `AUIPC`) 실행을 검증한 결과입니다.  
즉시값(imm)이 상위 비트에 올바르게 확장되고, AUIPC의 경우 Program Counter(PC) 값과 더해져 저장되는 것을 확인할 수 있습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/U-Type.png" alt="U-Type Verification" width="900"/>
</p>

---

### 🔹 J-Type & JALR 명령어 검증
아래는 `J-Type` (`JAL`) 및 `I-Type 변형` (`JALR`) 명령어 실행을 검증한 결과입니다.  
- **JAL** : 현재 PC 값 + offset → rd에 저장, PC는 분기된 주소로 갱신  
- **JALR** : rs1 + offset 값으로 PC를 갱신하고, 다음 명령어 주소를 rd에 저장  

파형에서 분기 및 점프 주소 계산이 정상적으로 수행됨을 확인할 수 있습니다.  

<p align="center">
  <img src="https://github.com/shhhhhhh1799/portfolio/blob/shhhhhhh1799-image/J-Type.png" alt="J & JALR Type Verification" width="900"/>
</p>

---

## 🔚 마무리

본 프로젝트를 통해 **멀티 사이클 CPU 아키텍처의 FSM 제어 설계**를 직접 구현하며,  
단일 사이클 대비 **하드웨어 자원 절약**과 **동작 단계별 제어**를 경험할 수 있었습니다.  

추후 확장 방향으로는  
- **파이프라인 구조** 전환
- **Hazard 처리 (Forwarding, Stall)**  
- **Interrupt/Exception Handling**  
- **FPGA 보드 구현 및 SoC 연동 실험**  
등을 추가하여 보다 실제 CPU 아키텍처와 유사하게 발전시킬 수 있습니다.  
