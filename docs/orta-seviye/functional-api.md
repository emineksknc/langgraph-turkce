# Functional API — @entrypoint & @task

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

Bu rehberde şimdiye kadar gördüğümüz her şey **Graph API** ile yazıldı: `StateGraph`, `add_node`, `add_edge`. LangGraph'ın bir de **Functional API**'si var — aynı yetenekleri (kalıcılık, human-in-the-loop, streaming) normal Python fonksiyonlarına yakın bir sözdizimiyle sağlayan alternatif bir yazım şekli.

## Neden ikinci bir API var?

Graph API, akışı **açıkça bir graf olarak** (node'lar + kenarlar) tanımlamanızı ister — karmaşık dallanma ve çoklu-ajan sistemlerinde bu netlik değerlidir. Ama basit, çoğunlukla **doğrusal** bir iş akışı için bir graf tanımlamak bazen gereğinden fazla tören (boilerplate) gibi hissettirebilir. Functional API, aynı akışı sıradan bir Python fonksiyonu gibi yazmanızı sağlar.

## @task ve @entrypoint

```python
from langgraph.func import entrypoint, task
from langgraph.checkpoint.memory import InMemorySaver

@task
def ozetle(metin: str) -> str:
    return llm.invoke(f"Özetle: {metin}").content

@task
def cevir(metin: str, dil: str) -> str:
    return llm.invoke(f"{dil} diline çevir: {metin}").content

@entrypoint(checkpointer=InMemorySaver())
def akis(girdi: dict) -> str:
    ozet = ozetle(girdi["metin"]).result()
    ceviri = cevir(ozet, girdi["dil"]).result()
    return ceviri

config = {"configurable": {"thread_id": "1"}}
akis.invoke({"metin": "...", "dil": "İngilizce"}, config)
```

- **`@task`**: tek bir işlem birimini işaretler — Graph API'deki bir **node**'a karşılık gelir. Checkpoint'lenebilir, retry edilebilir.
- **`@entrypoint`**: tüm akışın giriş noktasıdır — Graph API'deki `compile()` edilmiş `app`'e karşılık gelir. Checkpointer, config, human-in-the-loop hepsi burada bağlanır.

## Aynı yetenekler, farklı sözdizimi

Functional API, Graph API ile **aynı alt yapıyı** (Pregel çalışma zamanı) kullanır — bu yüzden şunların hepsi burada da çalışır:

```python
from langgraph.types import interrupt

@task
def onay_gerektiren_islem(tutar: float):
    if tutar > 500:
        karar = interrupt(f"{tutar} TL için onay gerekli")
        if karar != "evet":
            return "iptal edildi"
    return "işlendi"
```

- **Checkpointer & thread_id** → [Checkpointer & Kalıcılık](checkpointer.md) ile birebir aynı mantık
- **interrupt()** → [Human-in-the-loop](human-in-the-loop.md) ile birebir aynı mantık
- **Streaming** → `akis.stream(...)` aynı `stream_mode` seçenekleriyle çalışır

## Graph API mi, Functional API mi?

```mermaid
flowchart TD
    soru{Akış çoğunlukla doğrusal mı,<br/>az dallanma var mı?} -- evet --> func[Functional API]
    soru -- hayır, çok dallanma/döngü var --> soru2{Çoklu-ajan ya da<br/>karmaşık koşullu mantık mı?}
    soru2 -- evet --> graph[Graph API]
    soru2 -- hayır --> func
```

| Kriter | Graph API | Functional API |
|---|---|---|
| Akış şekli | Karmaşık dallanma, döngü, çoklu-ajan | Çoğunlukla doğrusal, az dallanma |
| Görsel netlik | Grafı `draw_mermaid()` ile görebilirsiniz | Kod okunarak anlaşılır, görsel graf çıktısı sınırlı |
| Öğrenme eğrisi | State şeması + node/edge kavramları | Normal Python fonksiyonu yazmaya yakın |
| Bu rehberdeki kullanım | [Örnek Proje](../ornek-proje/musteri-destek-botu.md) dahil çoğu sayfa | Bu sayfa |

!!! tip "İkisi karıştırılabilir mi?"
    Evet — bir `@task`'ın içinde bir Graph API grafı çağırabilir, ya da Graph API'deki bir node'un içinde `@task` kullanabilirsiniz. Çoğu ekip tek bir stille başlar ve ihtiyaç oldukça diğerini ödünç alır.

---

Sıradaki adım: [Checkpointer & Kalıcılık](checkpointer.md) — Graph API'de gördüğümüz gibi Functional API'de de aynı mantıkla çalışır.
