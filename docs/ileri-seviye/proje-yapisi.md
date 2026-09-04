# Proje Yapısı

<span class="badge badge-ileri">🔴 İLERİ / SENIOR</span>

Rehberdeki kod örnekleri tek dosyada gösterildi, ama gerçek bir LangGraph projesi büyüdükçe dosyaları organize etmek gerekir. Bu sayfa, [langgraph.json & CLI](deployment-cli.md) ile uyumlu, yaygın kullanılan bir klasör yapısını gösterir.

## Önerilen klasör yapısı

```
musteri-botu/
├── langgraph.json
├── pyproject.toml
├── .env
├── src/
│   └── musteri_botu/
│       ├── __init__.py
│       ├── graph.py          # StateGraph tanımı + compile()
│       ├── state.py          # State şeması (TypedDict)
│       ├── nodes/
│       │   ├── __init__.py
│       │   ├── router.py     # router node fonksiyonu
│       │   ├── chatbot.py
│       │   └── iade.py
│       ├── tools.py          # @tool ile işaretli araçlar
│       └── config.py         # RunnableConfig şeması, sabitler
└── tests/
    ├── test_router.py
    ├── test_iade.py
    └── test_graf_uctan_uca.py
```

## Neden node'ları ayrı dosyalara bölmeli?

- **Test edilebilirlik**: her node fonksiyonu kendi dosyasında, kendi testiyle eşleşir (bkz. [Test Yazma](../orta-seviye/testing.md))
- **Ekip çalışması**: farklı kişiler farklı node'lar üzerinde çakışmadan çalışabilir
- **Okunabilirlik**: `graph.py` sadece "hangi node'lar nasıl bağlanıyor" sorusuna odaklanır, node'ların iç mantığıyla karışmaz

## graph.py örneği

```python
from langgraph.graph import StateGraph, START, END
from musteri_botu.state import State
from musteri_botu.nodes.router import router
from musteri_botu.nodes.chatbot import chatbot
from musteri_botu.nodes.iade import iade_kontrol, iade_isle

builder = StateGraph(State)
builder.add_node("router", router)
builder.add_node("chatbot", chatbot)
builder.add_node("iade_kontrol", iade_kontrol)
builder.add_node("iade_isle", iade_isle)

builder.add_edge(START, "router")
builder.add_edge("iade_kontrol", "iade_isle")
builder.add_edge("iade_isle", "chatbot")
builder.add_edge("chatbot", END)

app = builder.compile()  # langgraph.json bu değişkeni referans alır
```

`langgraph.json` içindeki `"graphs"` alanı, tam olarak bu `app` değişkenini işaret eder:

```json
{
  "graphs": {
    "musteri_botu": "./src/musteri_botu/graph.py:app"
  }
}
```

## Birden fazla graf aynı projede

Büyük projelerde tek `langgraph.json` içinde birden fazla graf tanımlanabilir — ör. ana bot ile bir arka plan iş akışı ayrı graf olarak:

```json
{
  "graphs": {
    "musteri_botu": "./src/musteri_botu/graph.py:app",
    "gunluk_rapor": "./src/musteri_botu/rapor_graph.py:app"
  }
}
```

---

Sıradaki adım: [Örnek Proje — Müşteri Destek Botu](../ornek-proje/musteri-destek-botu.md) bu yapıyı tek dosyada özetler; gerçek projede yukarıdaki gibi bölmen önerilir.
