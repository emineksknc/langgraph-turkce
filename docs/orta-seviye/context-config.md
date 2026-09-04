# Config & Context Injection

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

State, grafın **çalışma zamanı verisidir** — konuşma boyunca değişir. Bazı bilgiler ise state'e ait değildir: kullanıcı kimliği, ortam ayarları, hangi model kullanılacağı gibi **her çağrıya özel ama grafın kendisinin parçası olmayan** parametreler. Bu tekniğe **config injection** (yapılandırma enjeksiyonu) denir ve `RunnableConfig` ile yapılır.

## Neden state içine koymuyoruz?

State, checkpointer tarafından kaydedilir ve grafın parçasıdır. Ama "bu isteği hangi kullanıcı yaptı" gibi bilgi genelde **konuşma geçmişinin bir parçası değil, çağrı bağlamının bir parçasıdır**. State'e karıştırmak, state şemasını gereksiz büyütür ve checkpointer'a gereksiz veri yazar.

## Node'a config geçirme

```python
from langchain_core.runnables import RunnableConfig

def chatbot(state: State, config: RunnableConfig):
    kullanici_id = config["configurable"].get("kullanici_id")
    model_adi = config["configurable"].get("model", "gpt-4o-mini")
    llm = ChatOpenAI(model=model_adi)
    return {"messages": [llm.invoke(state["messages"])]}
```

Çağrı sırasında:

```python
config = {
    "configurable": {
        "thread_id": "sohbet-42",   # checkpointer bunu kullanır
        "kullanici_id": "u-123",     # senin kendi ek parametren
        "model": "gpt-4o",
    }
}
app.invoke({"messages": [("user", "merhaba")]}, config)
```

!!! tip "thread_id de aslında configurable içinde"
    [Checkpointer](checkpointer.md) sayfasında gördüğünüz `thread_id`, aslında bu genel `configurable` mekanizmasının özel bir alanıdır. Kendi ek parametrelerinizi aynı sözlüğe ekleyebilirsiniz.

## Context şeması ile tip güvenliği

Serbest `config["configurable"]` sözlüğü yerine, hangi alanların bekleneceğini açıkça tanımlayabilirsiniz:

```python
from dataclasses import dataclass

@dataclass
class ContextSchema:
    kullanici_id: str
    model: str = "gpt-4o-mini"

graph = StateGraph(State, context_schema=ContextSchema)
```

Bu, büyük ekiplerde "bu graf hangi runtime parametreleri bekliyor?" sorusunu koda açıkça yazmanızı sağlar — dokümantasyon yükünü azaltır.

## Store ve config'in birlikte kullanımı

Çok kullanıcılı bir sistemde, `config`'ten okuduğunuz `kullanici_id` ile [Store](subgraph-store.md)'a erişmek yaygın bir desendir:

```python
def node_fn(state: State, config: RunnableConfig, *, store):
    kullanici_id = config["configurable"]["kullanici_id"]
    tercihler = store.get((kullanici_id, "tercihler"), "genel")
    ...
```

---

Sıradaki adım: [Test Yazma](testing.md).
