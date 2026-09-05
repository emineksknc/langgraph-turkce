# ToolNode & Hazır Ajanlar

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

**Tool** (araç), modelin çağırabileceği, dış dünyayla etkileşim kuran bir fonksiyondur (ör. hava durumu sorgulama, veritabanı okuma). Araç çağırma döngüsünü (chatbot → araç → chatbot) elle kurmak yerine LangGraph'ın hazır bileşenlerini kullanabilirsiniz.

## ToolNode + tools_condition

```python
from langgraph.prebuilt import ToolNode, tools_condition
from langchain_core.tools import tool

@tool
def hava_durumu(sehir: str) -> str:
    """Verilen şehrin hava durumunu döner."""
    return f"{sehir}: 24°C, açık"

tools = [hava_durumu]
llm_with_tools = llm.bind_tools(tools)

def chatbot(state: State):
    return {"messages": [llm_with_tools.invoke(state["messages"])]}

graph.add_node("chatbot", chatbot)
graph.add_node("tools", ToolNode(tools))
graph.add_conditional_edges("chatbot", tools_condition)
graph.add_edge("tools", "chatbot")
```

`tools_condition`, son mesajda `tool_calls` varsa `"tools"` node'una, yoksa `END`'e yönlendiren hazır bir karar fonksiyonudur — bir önceki sayfada elle yazdığımız `yon_bul` fonksiyonunun hazır hâli.

### Görsel: araç çağırma döngüsü

```mermaid
flowchart LR
    START((START)) --> chatbot[chatbot]
    chatbot -- tool_calls var --> tools[tools]
    chatbot -- tool_calls yok --> END((END))
    tools --> chatbot
```

`chatbot` ile `tools` arasındaki döngü, model gerektiği kadar araç çağırana kadar devam eder.

## Hazır ajan — tek satırda kurulum

**ReAct** (*Reason + Act* — "akıl yürüt ve eyle"), "düşün → araç çağır → sonucu değerlendir → gerekirse tekrar düşün" döngüsüne dayanan klasik ajan desenidir. Küçük/orta ölçekli görevler için grafı elle kurmanıza gerek yok — hazır bir fonksiyon bu deseni size sağlar:

!!! warning "create_react_agent deprecated edildi"
    LangGraph 1.0 öncesinde bu iş için `langgraph.prebuilt.create_react_agent` kullanılıyordu. **LangGraph v1 itibarıyla bu fonksiyon deprecated edildi** — yerine LangChain'in `create_agent` fonksiyonu öneriliyor (LangGraph runtime'ı üzerinde çalışır, middleware sistemiyle daha esnek). Eski kod tabanlarında/tutoriallerde hâlâ `create_react_agent` görebilirsiniz; yeni yazdığınız kodda aşağıdaki güncel yaklaşımı tercih edin.

```python
from langchain.agents import create_agent

agent = create_agent(
    model=llm,
    tools=[hava_durumu],
    system_prompt="Sen yardımsever bir asistansın.",
)
agent.invoke({"messages": [("user", "İstanbul'da hava nasıl?")]})
```

!!! note "Ne zaman elle graf kurmalı?"
    `create_agent` (eski adıyla `create_react_agent`), standart "düşün → araç çağır → cevapla" döngüsü için idealdir. Özel bir akış (birden fazla ajan, insan onayı, koşullu dallanma) gerekiyorsa, StateGraph'ı elle kurmak size çok daha fazla kontrol verir — bkz. [Multi-Agent Mimarileri](../ileri-seviye/multi-agent.md).

## Birden fazla araç

```python
tools = [hava_durumu, hesap_makinesi, veritabani_sorgula]
llm_with_tools = llm.bind_tools(tools)
```

Model, kullanıcının isteğine göre hangi aracı (ya da hiçbirini) çağıracağına kendisi karar verir; birden fazla aracı aynı anda da çağırabilir (`tool_calls` listesi birden fazla eleman içerebilir).

---

Sıradaki adım: LangGraph'ın ikinci yazım şekli — [Functional API](functional-api.md); atlamak istersen doğrudan [Checkpointer & Kalıcılık](checkpointer.md)'a geçebilirsin.
