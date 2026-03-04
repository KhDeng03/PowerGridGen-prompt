# Power Grid Model Generation Prompt Set

This repository provides the prompt template used in the paper **Power grid model generation based on the tool-augmented large language model**.

## 📌 Overview

In this work, we propose a power grid model generation agent based on large language models (LLMs) and external tools. The agent we built providing users with an intuitive way to generate power grid models according to their needs. Instead of directly generating a complete grid model in a single round, the agent decomposes the task into three stages and interacts with external tools to ensure physical validity.

### 🧠 Agent Architecture

The proposed agent performs power grid model generation in three sequential stages:

1. **Topology Generation** 
   The LLM extracts structured topology-related parameters from the prompt and calls a topology synthesis tool (in our work is `AutoSynGrid` from `MATPOWER`) to generate a valid network structure.

2. **Power Flow Data Insertion**
   The agent assigns generation, load, and branch parameters to complete the model.

3. **Power Flow Adjustment** 

   Dedicated power flow adjustment tools  are used to:

   - Ensure power flow convergence
   - Adjust operating conditions, to meet the user's requirement about the power flow characteristics

This multi-stage pipeline ensures that the generated grid models are not only structurally valid but also operationally feasible.

### 📄About the Template

The template consists of three components:

- **Instruction**: System prompt, which describes the task requirement.
- **Input**: User input, which specifies system configuration and operational characteristics.
- **Response**: The LLM response, structured power grid model.

Each sample is stored in JSON format as:

```json
{
  "instruction": "...",
  "input": "...",
  "response": "..."
}
```

Distinct prompt templates are designed for the three stages, and their implementations are available in the `templates/` directory.

## ⚙️ Example

Below is a simplified example illustrating how the proposed agent generates a power grid model.  For brevity, the full instruction templates of each stage are not shown.

### 🟢 User Request 1

> Generate a 39-bus transmission power system with an average node degree of 2.5.

#### Stage 1 — Topology Generation

**Input to LLM**

- Stage 1 instruction (topology generation template)
- User request

**LLM Output (Topology Structure)**

```json
{
  "bus": [
    {"bus_i": 1, "bus_type": 0},
    ...
  ],
  "branch": [
    {"fbus": 1, "tbus": 2},
    ...
  ]
}
```

The extracted topology-related parameters are forwarded to the topology synthesis tool (e.g., AutoSynGrid) to ensure structural validity.

#### Stage 2 — Power Flow Data Insertion

**Input to LLM**

- Stage 2 instruction (power flow completion template)
- Generated topology structure

**LLM Output (Initial Power Grid Model)**

```json
{
  "bus": [
    {"bus_i": 1, "Pd": 20, "Qd": 10, "...": "..."},
    ...
  ],
  "gen": [
    {"gen_bus": 1, "Pg": 200, "Qg": 100, "...": "..."},
    ...
  ],
  "branch": [
    {"fbus": 1, "tbus": 2, "r": 0.01, "x": 0.05, "b": 0, "...": "..."},
    ...
  ]
}
```

This stage completes load, generation, and branch parameters to form a steady-state grid model.

#### Stage 3 — Power Flow Adjustment

**Input to LLM**

- Stage 3 instruction (adjustment template)
- Initial power grid model

**Operation**

The agent invokes a power flow adjustment tool to ensure AC power flow convergence and satisfy operational constraints.

**Output**

A feasible power grid model in the same structured format as Stage 2.

------

### 🟢 User Request 2 (Operational Refinement)

> I would like the system to operate under economically optimal conditions.

**Operation**

The agent calls an operational adjustment tool to modify generator dispatch and achieve the desired economic operating state.

**Final Output**

An economically optimized and power-flow-feasible grid model.

## 📖 Citation

🌟 If you find this resource helpful, please consider starting this repository and cite our research:

```
@article{deng2025power,
  title={Power grid model generation based on the tool-augmented large language model},
  author={Deng, Kaihang and Zhou, Yanzhen and Zeng, Hongtai and Wang, Zhengcheng and Guo, Qinglai},
  journal={IEEE Transactions on Power Systems},
  year={2025},
  publisher={IEEE}
}
```
