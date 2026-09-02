# Subgraph & Store

## Subgraph — modüler graf tasarımı

Büyüyen bir graf yönetilemez hale gelmeden önce, mantıksal parçaları ayrı graflara bölüp ana grafa **node olarak** gömebilirsiniz.

```python
# Alt graf: belge özetleme akışı
alt_builder = StateGraph(AltState)
alt_builder.add_node("ozetle", ozetle_node)
alt_builder.add_edge(START, "ozetle")
alt_builder.add_edge("ozetle", END)
alt_graph = alt_builder.compile()

# Ana graf, alt grafı bir node gibi kullanır
ana_graph.add_node("ozet_akisi", alt_graph)
```

Subgraph'lar, ekip içinde farklı kişilerin farklı akışları bağımsız geliştirip test etmesini kolaylaştırır — tıpkı bir fonksiyonu başka bir fonksiyonun içinde kullanmak gibi.

!!! note "State şemaları farklı olabilir"
    Alt grafın state şeması, ana grafınkinden farklı olabilir; LangGraph, ortak alanları otomatik eşler. Tamamen farklı şemalar için ana graftaki node fonksiyonu içinde alt grafı manuel çağırıp state'i kendiniz dönüştürebilirsiniz.

## Store — uzun süreli, thread'ler arası hafıza

[Checkpointer](checkpointer.md) tek bir konuşmaya (`thread_id`) özeldir. `Store`, farklı konuşmalar arasında kalıcı olması gereken bilgiler içindir — kullanıcı tercihleri, geçmiş özetleri, öğrenilen gerçekler gibi.

```python
from langgraph.store.memory import InMemoryStore

store = InMemoryStore()

# Yazma: (namespace, key, value)
store.put(("kullanici123", "tercihler"), "dil", {"deger": "tr"})

# Okuma
sonuc = store.get(("kullanici123", "tercihler"), "dil")

# Arama (anlamsal arama için embedding destekli store'lar da mevcut)
sonuclar = store.search(("kullanici123", "tercihler"))
```

Store'u grafa bağlamak için `compile()` sırasında verirsiniz; node'lar içinden `config`/`store` parametresiyle erişilir.

```python
app = graph.compile(checkpointer=checkpointer, store=store)

def node_fn(state: State, *, store):
    tercih = store.get(("kullanici123", "tercihler"), "dil")
    ...
```

**Production'da** `InMemoryStore` yerine Postgres tabanlı (embedding destekli anlamsal arama dahil) kalıcı store kullanılması önerilir.

---

Orta seviye burada tamamlanıyor. Sıradaki bölüm: [İleri / Senior — Command & Send API](../ileri-seviye/send-command.md).
