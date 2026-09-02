# Streaming

LangGraph üç farklı streaming modu sunar; her biri farklı bir kullanım senaryosuna hizmet eder.

## stream_mode karşılaştırması

| Mod | Ne döner | Ne zaman kullanılır |
|---|---|---|
| `values` | Her adımdan sonra **tam state** | Debug, tüm state'i izlemek istediğinizde |
| `updates` | Sadece **değişen kısım** | Hangi node'un ne değiştirdiğini görmek |
| `messages` | LLM'den gelen **token akışı** | Kullanıcıya canlı yazı efekti göstermek (chat UI) |

```python
for chunk in app.stream({"messages": [("user", "merhaba")]}, config, stream_mode="updates"):
    print(chunk)
```

## Token bazlı akış (chat UI için)

```python
for msg, metadata in app.stream(
    {"messages": [("user", "kısa bir şiir yaz")]},
    config,
    stream_mode="messages",
):
    print(msg.content, end="", flush=True)
```

## Olay bazlı akış — astream_events

Retriever, tool çağrısı, LLM token'ı gibi grafın **her türlü olayını** ayrı ayrı yakalamak isterseniz (ör. UI'da "araç çağrılıyor..." göstermek için):

```python
async for ev in app.astream_events({"messages": [...]}, config, version="v2"):
    if ev["event"] == "on_chat_model_stream":
        print(ev["data"]["chunk"].content, end="")
    elif ev["event"] == "on_tool_start":
        print(f"\n[araç çağrılıyor: {ev['name']}]")
```

!!! tip "FastAPI ile Server-Sent Events"
    Bir chat arayüzü kuruyorsanız, `stream_mode="messages"` çıktısını doğrudan bir SSE (Server-Sent Events) endpoint'ine bağlayarak frontend'e token token gönderebilirsiniz.

---

Sıradaki adım: kritik kararlarda insan onayı almak için [Human-in-the-loop](human-in-the-loop.md).
