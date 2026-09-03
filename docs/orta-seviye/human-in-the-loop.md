# Human-in-the-loop

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

Bazı adımlar (ör. ödeme yapma, e-posta gönderme, veri silme) otomatik değil, **insan onayıyla** ilerlemelidir. LangGraph, checkpointer sayesinde grafı belirli bir node'dan önce/sonra durdurabilir.

## interrupt_before / interrupt_after

```python
app = graph.compile(
    checkpointer=checkpointer,
    interrupt_before=["tools"],  # tools node'undan önce dur
)

config = {"configurable": {"thread_id": "islem-1"}}

# 1. Graf "tools"tan önce durur
app.invoke({"messages": [("user", "500 TL öde")]}, config)

# 2. Kullanıcıya/operatöre onay sorulur (UI, Slack mesajı, vb.)

# 3. Onay verildiyse, kaldığı yerden devam et
app.invoke(None, config)
```

`None` ile `invoke` çağrısı, "state'i değiştirmeden kaldığın yerden devam et" anlamına gelir.

## Durma noktasında state'i değiştirme

Devam etmeden önce state'i güncelleyebilirsiniz — ör. kullanıcı işlem tutarını değiştirdiyse:

```python
app.update_state(config, {"tutar": 400})
app.invoke(None, config)
```

## interrupt() fonksiyonu ile ince kontrol (yeni API)

Daha güncel LangGraph sürümlerinde, node'un içinden doğrudan `interrupt()` çağırarak, hangi bilginin insana gösterileceğini ve dönen cevabın nasıl işleneceğini programatik olarak kontrol edebilirsiniz:

```python
from langgraph.types import interrupt, Command

def onay_node(state: State):
    karar = interrupt({"soru": "Bu işlemi onaylıyor musunuz?", "tutar": state["tutar"]})
    return {"onaylandi": karar == "evet"}

# Devam ettirmek için:
app.invoke(Command(resume="evet"), config)
```

!!! tip "Ne zaman hangisi?"
    `interrupt_before/after`, node bazında kaba bir durma noktasıdır — hızlı kurulur. `interrupt()`, node'un içinden hangi bilginin insana gideceğini ve cevabın nasıl işleneceğini tanımlamanıza izin verir — daha esnek ama biraz daha fazla kod gerektirir.

---

Sıradaki adım: [Subgraph & Store](subgraph-store.md) ile modüler graf tasarımı.
