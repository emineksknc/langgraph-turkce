# Subgraph & Store

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

## Subgraph — modüler graf tasarımı

**Subgraph** (alt graf), büyüyen bir graf yönetilemez hale gelmeden önce mantıksal parçalara bölüp ana grafa **node olarak** gömebileceğiniz, bağımsız çalışabilen küçük bir graftır.

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

!!! abstract "Kısa süreli vs uzun süreli hafıza"
    LangGraph dokümantasyonunda hafıza ikiye ayrılır: **kısa süreli hafıza** (*short-term memory*) — tek bir thread'in state'i, [checkpointer](checkpointer.md) tarafından tutulur; ve **uzun süreli hafıza** (*long-term memory*) — thread'ler arası kalıcı bilgi, aşağıda anlatılan `Store` tarafından tutulur. "Bu konuşmada ne konuştuk" kısa süreli, "bu kullanıcı hakkında ne biliyorum" uzun süreli hafızadır.

[Checkpointer](checkpointer.md) tek bir konuşmaya (`thread_id`) özeldir. **Store** (depo), farklı konuşmalar arasında kalıcı olması gereken bilgiler içindir — kullanıcı tercihleri, geçmiş özetleri, öğrenilen gerçekler gibi.

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

### Anlamsal arama (semantic search — embedding destekli Store)

Basit `get`/`put` ile sadece **tam eşleşen anahtarları** okuyabilirsiniz. Gerçek bir "uzun süreli hafıza" sisteminde genelde **anlamsal olarak benzer** kayıtları bulmak istersiniz — ör. "kullanıcı deniz kenarı tatilini sever" bilgisini, "tatil önerisi" sorgusuyla bulabilmek. Bu yönteme **semantic search** (anlamsal arama) denir ve bir **embedding** (gömme vektörü — metnin anlamını sayısal olarak temsil eden vektör) modeline dayanır. Bunun için `Store`'u bir embedding modeliyle yapılandırırsınız:

```python
from langgraph.store.postgres import PostgresStore
from langchain_openai import OpenAIEmbeddings

store = PostgresStore.from_conn_string(
    DB_URI,
    index={
        "embed": OpenAIEmbeddings(model="text-embedding-3-small"),
        "dims": 1536,
        "fields": ["metin"],  # hangi alan(lar) embed edilecek
    },
)
store.setup()

store.put(("kullanici123", "notlar"), "not-1", {"metin": "deniz kenarı tatillerini seviyor"})

# Anlamsal arama — tam eşleşme değil, anlam benzerliği
sonuclar = store.search(("kullanici123", "notlar"), query="tatil önerisi ne olabilir?", limit=3)
```

!!! example "Tipik kullanım"
    Bir müşteri destek botunun, kullanıcının geçmiş konuşmalarında bahsettiği tercihleri (anahtar kelime eşleşmesi olmadan) hatırlayıp öneriye dahil etmesi — klasik "long-term memory" deseni.

---

Orta seviye çalışma zamanı konuları burada tamamlanıyor. Sıradaki adım: [Config & Context Injection](context-config.md).

📄 Bu seviyenin özetini PDF olarak indir: [Orta Seviye Cheatsheet](../assets/cheatsheets/langgraph-orta-seviye-cheatsheet.pdf)
