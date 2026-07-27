# Runnable Interface

## What is a Runnable?

A Runnable is any LangChain component that can be executed.

Every Runnable follows a common interface:

```python
invoke(input) -> output
```

A Runnable:

- Accepts an input.
- Performs a specific task.
- Returns an output.

Since every component follows the same interface, they can be connected together.

---

# Example

## Step 1 - Create a PromptTemplate

```python
from langchain_core.prompts import PromptTemplate

prompt = PromptTemplate.from_template(
    "Explain {topic} in simple terms."
)
```

---

## Step 2 - Create an LLM

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4.1")
```

---

## Step 3 - Create an Output Parser

```python
from langchain_core.output_parsers import StrOutputParser

parser = StrOutputParser()
```

---

## Step 4 - Create a Chain

```python
chain = prompt | llm | parser
```

---

## Step 5 - Execute

```python
result = chain.invoke({
    "topic": "AI"
})
```

---

# Internal Execution

```
User Input
{
    "topic": "AI"
}
        │
        ▼
PromptTemplate.invoke()
Template:
"Explain {topic} in simple terms."

Formatted Prompt:
"Explain AI in simple terms."

        │
        ▼
ChatOpenAI.invoke()

        │
        ▼
OpenAI API

        │
        ▼
AIMessage(
    content="Artificial Intelligence (AI) is..."
)

        │
        ▼
StrOutputParser.invoke()

        │
        ▼
"Artificial Intelligence (AI) is..."

        │
        ▼
Final Result
```

---

## What LangChain does internally

```python
formatted_prompt = prompt.invoke({
    "topic": "AI"
})

ai_message = llm.invoke(formatted_prompt)

result = parser.invoke(ai_message)
```

The developer only writes:

```python
result = chain.invoke({
    "topic": "AI"
})
```

LangChain automatically executes each Runnable one after another.

---

# Why invoke()?

Different LLM providers expose different APIs.

Without LangChain:

```python
openai.chat.completions.create(...)
google.generate_content(...)
anthropic.messages.create(...)
```

With LangChain:

```python
llm.invoke(...)
```

LangChain provides a common interface, making it easy to switch LLM providers without changing the rest of the application.

---

# Key Takeaways

- Everything in LangChain is built around Runnables.
- Every Runnable exposes `invoke()`.
- `|` creates a pipeline.
- The output of one Runnable becomes the input of the next Runnable.
- A single `chain.invoke()` executes the entire pipeline.