# Checkpointer & Kalıcılık

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

Checkpointer, grafın her adımdan sonraki state'ini kaydeder. Bu sayede:

- Kesintiden sonra kaldığı yerden devam edebilirsiniz
- Aynı "thread" (konuşma) için geçmişi hatırlarsınız
- [Human-in-the-loop](human-in-the-loop.md) ve [time-travel](../ileri-seviye/time-travel.md) mümkün olur

## Geliştirme: MemorySaver

```python
from langgraph.checkpoint.memory import MemorySaver

app = graph.compile(checkpointer=MemorySaver())

config = {"configurable": {"thread_id": "kullanici-42"}}
app.invoke({"messages": [("user", "merhaba")]}, config)
app.invoke({"messages": [("user", "adımı hatırlıyor musun?")]}, config)
```

`thread_id`, hangi konuşmaya ait olduğunu belirtir — aynı `thread_id` ile yapılan her `invoke` çağrısı, önceki state'in üzerine devam eder.

!!! danger "MemorySaver sadece geliştirme içindir"
    `MemorySaver`, state'i sadece bellekte tutar — süreç yeniden başladığında **tüm veri kaybolur**. Production'da mutlaka kalıcı bir backend kullanın.

## Production: Postgres / SQLite

=== "Postgres"

    ```python
    from langgraph.checkpoint.postgres import PostgresSaver

    with PostgresSaver.from_conn_string(DB_URI) as checkpointer:
        checkpointer.setup()  # ilk çalıştırmada tabloları oluşturur
        app = graph.compile(checkpointer=checkpointer)
    ```

=== "SQLite"

    ```python
    from langgraph.checkpoint.sqlite import SqliteSaver

    with SqliteSaver.from_conn_string("sohbetler.db") as checkpointer:
        app = graph.compile(checkpointer=checkpointer)
    ```

## Store — thread'ler arası uzun süreli hafıza

Checkpointer, **tek bir thread'e** özel kısa süreli hafızadır. Birden fazla konuşma arasında kalıcı olması gereken bilgiler (ör. kullanıcı tercihleri) için `Store` kullanılır:

```python
from langgraph.store.memory import InMemoryStore

store = InMemoryStore()
store.put(("kullanici123",), "tercih", {"dil": "tr"})
sonuc = store.get(("kullanici123",), "tercih")
```

Daha fazla detay için: [Subgraph & Store](subgraph-store.md).

---

Sıradaki adım: kullanıcıya token token cevap göstermek için [Streaming](streaming.md).
