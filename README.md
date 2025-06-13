# Multi_Agent_concierge_system


## 🎯 Objective

To build a **multi-agent concierge system** using **LlamaIndex's Workflow API** that intelligently manages user inputs, routes them to the correct agent (like Add or Multiply), and executes tools (functions) with optional human approval gating.

---

## 🛠️ Tools & Technologies

- **Python 3.11+**
- **LlamaIndex** (Workflows, Agents, Tools)
- **Groq LLM** (`llama3-70b-8192`)
- **Pydantic** – for validating Agent Configs
- **FunctionTool & ToolSelection** – to wrap native Python functions
- **QueryEngineTool** – LlamaIndex's tool wrapper
- **Chat-based Function Calling** – via Groq or compatible LLMs

---

## 🔄 End-to-End Workflow

### 🔹 Part 1: Basic Agent Loop

**Architecture**:  
`User Input ➜ Agent Workflow ➜ LLM Reasoning ➜ Tool Call ➜ Tool Execution ➜ Tool Result ➜ LLM Follow-up ➜ User`

**Workflow Components**:
- **AgentConfig**: Defines name, system prompt, and tools.
- **FunctionTool**: Wraps Python functions like `add_two_numbers`.
- **Groq LLM**: Handles reasoning + tool calling.
- **Workflow Steps**:
  - `setup`: Stores LLM, message, agent config, chat history
  - `speak_with_agent`: LLM call to reason over prompt & history
  - `handle_tool_call`: Executes selected tool
  - `aggregate_tool_results`: Feeds result back to LLM

**System Prompt Enforcement**:
Each agent follows a strict `system_prompt`, allowing only its dedicated task (e.g., add-only agent). If the task doesn't match, it returns a rejection message.

---

### 🔹 Part 2: Human-in-the-Loop (HITL) Agent Workflow

**Architecture**:  
`User ➜ Agent ➜ LLM ➜ Tool Call ➜ Human Check ➜ If Approved: Execute ➜ Else: Return`

**Additions Over Part-1**:
- Emits a `ToolRequestEvent` for tools marked as requiring approval.
- Waits for `ToolApprovedEvent` before tool execution.
- Handles rejection by responding with a predefined message.

---

### 🔹 Part 3: HITL Multi-Agent Orchestration

**Architecture**:  
`User ➜ Concierge Agent ➜ LLM ➜ Orchestrator ➜ Agent Selection ➜ Tool Call ➜ HITL (Optional) ➜ Execution ➜ Result ➜ Final LLM Response`

**Workflow Components**:
- **Multiple Agents**: E.g., AdditionAgent, MultiplicationAgent
- **FunctionTool**: Python functions like `add_two_numbers`, `multiply_two_numbers`
- **Orchestrator**:
  - Selects the correct agent based on `agent descriptions` and `user intent`
- **Workflow Steps**:
  - `setup`: Prepares agent configs, messages, state
  - `speak_with_agent`: Prompts LLM, initiates reasoning
  - `handle_tool_approval`: Waits for human decision
  - `handle_tool_call`: Executes tools and returns result
  - `aggregate_tool_results`: Appends results to chat history
  - `orchestrator`: Chooses agent and hands off control

---

## ✅ Features

- 🔒 **Strict Task Enforcement**: via system prompts
- 🔁 **Multi-agent collaboration**: Agents perform only their assigned roles (e.g., Add or Multiply)
- 🔧 **Tool Execution**: Via LLM-backed function calling (FunctionTool)
- 👤 **Optional Human Approval (HITL)**: Secure gating before sensitive operations
- 🧠 **LLM Reasoning Loop**: Continuously loops through reasoning → tool calling → reasoning for refined responses
- 🧭 **Orchestrator**: Smart agent router that delegates to the right specialist agent based on intent


---

## 🧪 Sample Prompt Scenarios

1. **Valid Input (Addition)**  
   **Prompt**: `"What is 12 + 5?"`  
   ➤ Routed to Addition Agent ➤ Tool Executed ➤ Result: `17`

2. **Invalid Input (Unassigned Task)**  
   **Prompt**: `"What is the capital of Canada?"`  
   ➤ Rejected by agent (based on system prompt) ➤ Returns: `"Sorry, I can only help with addition."`

3. **HITL Input (Needs Approval)**  
   **Prompt**: `"Please add 999 + 1"`  
   ➤ Triggers HITL ➤ Waits for approval ➤ Executes after confirmation

---

## 🧠 Key Concepts

- **System Prompt**: Agent behavior guardrail.
- **ToolSelection**: LLM-chosen callable tools.
- **Workflow Events**: `ToolCallEvent`, `ToolResultEvent`, `ToolRequestEvent`, etc.
- **Context Management**: Shared memory between steps using `Context` in LlamaIndex.
- **Agent Specialization**: Prevents scope overlap or unauthorized reasoning.


## 🏁 Conclusion

This project showcases a **scalable, modular, and intelligent multi-agent assistant** that:

- Routes user queries using LLM reasoning
- Enforces strict agent behavior via system prompts
- Uses Groq LLM for fast, chat-aware function execution
- Integrates human approval seamlessly into agent loops



