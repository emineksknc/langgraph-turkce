# Production & Deployment

<span class="badge badge-ileri">🔴 İLERİ / SENIOR</span>

## Durable execution (dayanıklı çalıştırma) — genel çerçeve

Production'da hata yönetimi, checkpointer ve retry ayrı ayrı konular gibi görünebilir ama aslında hepsi tek bir kavramın parçasıdır: **durable execution** (dayanıklı çalıştırma) — yani bir çalıştırmanın, çökme/yeniden başlatma/geçici hata gibi kesintilere rağmen kaldığı yerden devam edebilmesi. LangGraph'ta bu üç bileşenle sağlanır:

1. **[Checkpointer](../orta-seviye/checkpointer.md)** — her adımdan sonra state'i kalıcı olarak kaydeder
2. **Retry policy** (aşağıda) — geçici hatalarda otomatik yeniden dener
3. **[Human-in-the-loop](../orta-seviye/human-in-the-loop.md)** — beklenen "duraklamaları" (insan onayı) yönetir

Bu üçü birlikte düşünüldüğünde, "production'a hazır mıyım?" sorusu aslında "çalıştırmam gerçekten dayanıklı mı?" sorusuna dönüşür.

## Hata yönetimi ve retry

Harici API çağıran node'larda geçici hataları (rate limit, network) tolere etmek için node bazlı **retry policy** (yeniden deneme politikası) tanımlayın:

```python
from langgraph.pregel import RetryPolicy

graph.add_node(
    "arac_cagir",
    arac_node,
    retry=RetryPolicy(max_attempts=3, backoff_factor=2),
)
```

## Recursion limit (döngü üst sınırı)

Döngüsel graflarda sonsuz döngüyü önlemek için üst sınır koyun — özellikle multi-agent sistemlerde kritik:

```python
app.invoke(input, config={"recursion_limit": 25})
```

## Observability — gün 1'den itibaren kurun

Production'da neyin, neden yanlış gittiğini görmeden debug etmek neredeyse imkansızdır.

```python
import os
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_PROJECT"] = "bot-prod"
# app.invoke() çağrıları otomatik olarak LangSmith'e trace gönderir
```

Alternatif/tamamlayıcı olarak Langfuse `CallbackHandler` da grafın her node'unu ayrı span olarak izler.

## Checkpointer backend'i

`MemorySaver` sadece geliştirme içindir. Production'da Postgres veya Redis tabanlı kalıcı bir checkpointer kullanın (bkz. [Checkpointer sayfası](../orta-seviye/checkpointer.md)) — süreç yeniden başladığında tüm konuşma geçmişini kaybetmemek için şarttır.

## Deployment seçenekleri

| Seçenek | Ne zaman uygun |
|---|---|
| **LangGraph Platform** (managed) | Hızlı başlamak, altyapı yönetmek istemiyorsanız |
| **Kendi FastAPI sarmalayıcınız** | Tam kontrol, mevcut altyapınıza entegrasyon |
| **Docker + Kubernetes** | Kurumsal ölçek, çoklu ortam |

Basit bir FastAPI sarmalayıcı örneği:

```python
from fastapi import FastAPI
from pydantic import BaseModel

app_api = FastAPI()

class Istek(BaseModel):
    mesaj: str
    thread_id: str

@app_api.post("/sohbet")
def sohbet(istek: Istek):
    config = {"configurable": {"thread_id": istek.thread_id}}
    sonuc = app.invoke({"messages": [("user", istek.mesaj)]}, config)
    return {"cevap": sonuc["messages"][-1].content}
```

## Maliyet ve performans kontrol listesi

- [ ] Her döngü turunun token maliyetini biliyor musunuz? `recursion_limit` koydunuz mu?
- [ ] Checkpointer prod'da kalıcı bir backend'e mi bağlı?
- [ ] Trace'leriniz node bazında etiketli mi (hangi ajan/adım neye mal oluyor)?
- [ ] Kritik/geri alınamaz aksiyonlar için [human-in-the-loop](../orta-seviye/human-in-the-loop.md) var mı?
- [ ] Harici API çağıran node'larda retry/timeout tanımlı mı?

---

Bu, ileri seviye bölümünün son sayfasıdır. LangGraph Platform ile deploy etmek istersen: [langgraph.json & CLI](deployment-cli.md).

📄 Bu seviyenin özetini PDF olarak indir: [İleri Seviye Cheatsheet](../assets/cheatsheets/langgraph-ileri-seviye-cheatsheet.pdf)
