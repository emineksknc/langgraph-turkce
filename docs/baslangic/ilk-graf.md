# İlk Grafını Kur — Basit Bir Chatbot

Bu sayfada, mesaj geçmişini hatırlayan basit bir chatbot grafı kuracağız.

## 1. State tanımı

```python
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: Annotated[list, add_messages]
```

## 2. Node fonksiyonu

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

def chatbot(state: State):
    return {"messages": [llm.invoke(state["messages"])]}
```

## 3. Grafı kur ve derle

```python
from langgraph.graph import StateGraph, START, END

builder = StateGraph(State)
builder.add_node("chatbot", chatbot)
builder.add_edge(START, "chatbot")
builder.add_edge("chatbot", END)

app = builder.compile()
```

## 4. Çalıştır

```python
sonuc = app.invoke({"messages": [("user", "Türkiye'nin başkenti neresi?")]})
print(sonuc["messages"][-1].content)
```

## Grafı görselleştirme

Geliştirme sırasında grafın yapısını görmek faydalıdır:

```python
from IPython.display import Image
Image(app.get_graph().draw_mermaid_png())
```

Jupyter dışında çalışıyorsanız, `app.get_graph().draw_mermaid()` ile Mermaid metnini alıp [mermaid.live](https://mermaid.live) üzerinde görebilirsiniz.

---

Sıradaki adım: gerçek ajanlarda vazgeçilmez olan [Koşullu Kenarlar](kosullu-kenarlar.md).
