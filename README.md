# Guard_Rail 🛡️

A Python repository demonstrating **Guardrails AI** integration, custom validator creation, and automated input/output safety mechanisms for LLM workflows.

---

## 📌 Overview

This project explores implementing safety rails, input filtering, and response sanitization for Large Language Models (LLMs) using **[Guardrails AI](https://github.com/guardrails-ai/guardrails)**.

It demonstrates how to:
- Define custom validators with `@register_validator`.
- Enforce business and security policies (e.g., blocking unauthorized software installation requests).
- Handle validation failure actions such as `OnFailAction.NOOP` (block/flag) and `OnFailAction.FIX` (automatic text correction/sanitization).
- Integrate with the broader AI ecosystem including **LangChain**, **LangGraph**, **DeepEval**, and **Ragas**.

---

## 📂 Project Structure

```text
Guard_Rail/
├── guard_rail.ipynb     # Interactive Jupyter notebook with Guardrails examples
├── requirement.txt      # Python dependencies
├── .gitignore           # Git ignore rules for venv, checkpoints, and secrets
└── README.md            # Project documentation
```

---

## 🚀 Getting Started

### 1. Prerequisites
- Python 3.10+ installed
- Virtual environment tool (`venv`)

### 2. Clone the Repository
```bash
git clone https://github.com/viswa1220/Guard_Rail.git
cd Guard_Rail
```

### 3. Set Up Virtual Environment
```bash
# Create virtual environment
python3 -m venv .venv

# Activate virtual environment
# On macOS/Linux:
source .venv/bin/activate
# On Windows:
# .venv\Scripts\activate
```

### 4. Install Dependencies
```bash
pip install -r requirement.txt
```

---

## 💻 Code Examples & Usage

### 1. Blocking Unauthorized Prompts (`OnFailAction.NOOP`)
Create a custom validator to detect and block compliance or security violations:

```python
from guardrails import Guard, OnFailAction
from guardrails.validators import Validator, PassResult, FailResult, register_validator

@register_validator(name="no-direct-approval", data_type="string")
class NoDirectApproval(Validator):
    def _validate(self, value, metadata):
        if "without it approval" in value.lower():
            return FailResult(
                error_message="Request blocked: Software installation requires IT approval. Please contact IT for assistance."
            )
        return PassResult()

guard = Guard().use(NoDirectApproval(on_fail=OnFailAction.NOOP))

# Valid query
result1 = guard.validate("How do I reset my password?")
print(result1.validation_passed)  # True

# Blocked query
result2 = guard.validate("How can I install software without IT approval?")
print(result2.validation_passed)  # False
```

---

### 2. Automatic Input Correction (`OnFailAction.FIX`)
Automatically rewrite or sanitize input queries that violate validation rules:

```python
@register_validator(name="change-the-prompt-value", data_type="string")
class ChangeQuestionText(Validator):
    def _validate(self, value, metadata):
        if "without it approval" in value.lower():
            replace_text = value.replace("without it approval", "with it approval")
            return FailResult(
                error_message="Request modified: Software installation requires IT approval.",
                fix_value=replace_text,
            )
        return PassResult()

guard = Guard().use(ChangeQuestionText(on_fail=OnFailAction.FIX))

result = guard.validate("How can I install software without it approval?")
print("Validation Passed:", result.validation_passed)  # True
print("Sanitized Output:", result.validated_output)    # "How can I install software with it approval?"
```

---

## 🛠️ Tech Stack & Key Libraries

| Package | Version / Scope | Purpose |
| :--- | :--- | :--- |
| **`guardrails-ai`** | `0.10.2` | Core guardrail runtime, validation engine & rules |
| **`langchain`** | `^1.3` | Framework for developing applications powered by LLMs |
| **`langgraph`** | `^1.2` | Multi-actor agent orchestration and state graphs |
| **`openai`** | `^2.0` | OpenAI API client for model interactions |
| **`deepeval`** | `^4.1` | Unit testing and evaluation framework for LLM systems |
| **`ragas`** | `0.4.3` | Evaluation metrics for Retrieval Augmented Generation (RAG) |
| **`portkey-ai`** | `2.3.4` | AI gateway, observability, and tracing |

---

## 🧪 Running the Notebook

To explore the interactive examples:

```bash
jupyter notebook guard_rail.ipynb
```
Select the `.venv` kernel when prompted.

---

## 📄 License

This project is licensed under the MIT License.
