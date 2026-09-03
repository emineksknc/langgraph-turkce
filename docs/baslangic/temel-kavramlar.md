# Temel Kavramlar — State, Node, Edge

<span class="badge badge-baslangic">🟢 BAŞLANGIÇ</span>

## Neden LangGraph?

LangChain'deki LCEL zincirleri (`prompt | llm | parser`) **doğrusaldır**: A adımından B'ye, B'den C'ye gider. Ama gerçek ajan sistemlerinde ihtiyacınız olan şey genellikle şudur:

- Bir aracı çağır → sonucu değerlendir → **gerekirse tekrar çağır** (döngü)
- Duruma göre **farklı bir yola** sap (dallanma)
- Uzun süren bir işlemi **yarıda kesip devam ettirebilmek** (kalıcılık)

LangGraph bu üç ihtiyacı karşılamak için tasarlanmış, graf tabanlı bir orkestrasyon kütüphanesidir.

## State (Durum)

State, grafın tüm node'ları arasında akan, paylaşılan veri yapısıdır. Genellikle bir `TypedDict` olarak tanımlanır:

```python
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: Annotated[list, add_messages]
    kullanici_id: str
```

!!! warning "Reducer'lar önemli"
    Varsayılan olarak bir node, state alanını **üzerine yazar**. `Annotated[list, add_messages]` gibi bir *reducer* tanımlarsanız, yeni değer eskisinin üzerine yazılmaz, **ona eklenir**. Mesaj geçmişi gibi büyüyen veriler için reducer kullanmazsanız her node'da geçmişi kaybedersiniz.

## Node (Düğüm)

Her node, state'i parametre olarak alan ve state'e eklenecek **kısmi güncellemeyi** döndüren bir Python fonksiyonudur:

```python
def chatbot(state: State):
    yanit = llm.invoke(state["messages"])
    return {"messages": [yanit]}  # tüm state değil, sadece güncellenen kısım
```

## Edge (Kenar)

Kenarlar, node'lar arasındaki akışı tanımlar.

- **Sabit kenar**: her zaman aynı sıradaki node'a gider — `graph.add_edge("a", "b")`
- **Koşullu kenar**: state'e bakarak hangi node'a gidileceğine karar verir — `graph.add_conditional_edges(...)`

`START` ve `END`, grafın giriş ve çıkış noktalarını belirten özel düğümlerdir.

## Grafı derleme

```python
from langgraph.graph import StateGraph, START, END

graph = StateGraph(State)
graph.add_node("chatbot", chatbot)
graph.add_edge(START, "chatbot")
graph.add_edge("chatbot", END)

app = graph.compile()
app.invoke({"messages": [("user", "merhaba")]})
```

Bu, en basit tek-node grafıdır. Gerçek dünyada ajanlar genelde araç çağırma, değerlendirme ve döngü node'ları içerir — bunu bir sonraki sayfada kuracağız: [İlk Grafını Kur](ilk-graf.md).
