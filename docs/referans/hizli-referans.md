# Hızlı Referans Tablosu

!!! tip "PDF olarak indir"
    Bu tablonun ve ilgili konuların 2 sayfalık özet kartları: [🟢 Başlangıç](../assets/cheatsheets/langgraph-baslangic-cheatsheet.pdf) · [🟡 Orta Seviye](../assets/cheatsheets/langgraph-orta-seviye-cheatsheet.pdf) · [🔴 İleri Seviye](../assets/cheatsheets/langgraph-ileri-seviye-cheatsheet.pdf)

| Kavram | Ne işe yarar | Import |
|---|---|---|
| `StateGraph` | Graf tanımı | `langgraph.graph` |
| `START` / `END` | Giriş/çıkış düğümleri | `langgraph.graph` |
| `add_messages` | Mesaj listesi reducer'ı | `langgraph.graph.message` |
| `ToolNode` | Hazır araç çalıştırıcı node | `langgraph.prebuilt` |
| `tools_condition` | Hazır araç-çağırma karar fonksiyonu | `langgraph.prebuilt` |
| `create_react_agent` | Hazır ReAct ajanı | `langgraph.prebuilt` |
| `MemorySaver` | Bellek içi checkpointer (sadece dev) | `langgraph.checkpoint.memory` |
| `PostgresSaver` / `SqliteSaver` | Kalıcı checkpointer | `langgraph.checkpoint.*` |
| `InMemoryStore` | Uzun süreli hafıza (dev) | `langgraph.store.memory` |
| `Command` | Yönlendirme + state güncelleme | `langgraph.types` |
| `Send` | Dinamik paralel dağıtım (map-reduce) | `langgraph.types` |
| `interrupt` / `interrupt_before/after` | Human-in-the-loop | `langgraph.types` / `compile()` |
| `RetryPolicy` | Node bazlı retry | `langgraph.pregel` |
| `get_state_history` | Time-travel / debug | `app` metodu |
| `astream_events` | Olay bazlı (retriever/tool/llm) akış | `app` metodu |

## Sık yapılan hatalar

!!! failure "Reducer eksikliği"
    Mesaj listesi gibi büyüyen state alanlarında `Annotated[list, add_messages]` kullanmazsanız, her node state'i sıfırdan yazar ve geçmiş kaybolur.

!!! failure "path_map olmadan koşullu kenar"
    `add_conditional_edges` içinde `path_map` vermezseniz, karar fonksiyonunun döndürdüğü string'in bir node adıyla birebir eşleşmesi gerekir — yazım hataları sessiz kalabilir.

!!! failure "recursion_limit koymamak"
    Döngü içeren graflarda üst sınır koymazsanız, mantık hatası durumunda sonsuz döngüye girip maliyeti patlatabilirsiniz.

!!! failure "MemorySaver'ı production'a taşımak"
    Süreç yeniden başladığında (deploy, crash, ölçekleme) tüm konuşma geçmişi kaybolur — kalıcı bir backend'e (Postgres/Redis) geçin.

---

*Bu sayfa canlı bir referanstır — yeni öğrendiğiniz bir detayı buraya PR ile ekleyebilirsiniz.*
