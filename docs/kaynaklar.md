# Kaynaklar

Bu sayfa, rehberdeki her ana konunun **resmi LangGraph dokümantasyonundaki** karşılığına tek noktadan erişim sağlar. Bir konuyu derinlemesine incelemek, en güncel API detaylarını görmek ya da bu rehberde bir eksik/hata bulduğunda kaynağı karşılaştırmak istediğinde buraya bakabilirsin.

!!! info "Neden her sayfada değil de burada?"
    Sayfa içlerine dağıtılmış onlarca link hem okuma akışını böler hem de resmi dokümantasyonun URL yapısı değiştiğinde (daha önce de değişti) bakımı zorlaştırır. Bunun yerine eşlemeyi tek bir yerde tutuyoruz.

## Genel

| Bu rehberde | Resmi kaynak |
|---|---|
| [Kurulum](baslangic/kurulum.md) | [Install LangGraph](https://docs.langchain.com/oss/python/langgraph/install) |
| [Temel Kavramlar](baslangic/temel-kavramlar.md), [İlk Grafını Kur](baslangic/ilk-graf.md) | [Graph API overview](https://docs.langchain.com/oss/python/langgraph/graph-api), [Quickstart](https://docs.langchain.com/oss/python/langgraph/quickstart) |
| [Workflows vs Agents](baslangic/workflows-vs-agents.md) | [Workflows and agents](https://docs.langchain.com/oss/python/langgraph/workflows-agents) |
| [Functional API](orta-seviye/functional-api.md) | [Functional API overview](https://docs.langchain.com/oss/python/langgraph/functional-api), [Use the functional API](https://docs.langchain.com/oss/python/langgraph/use-functional-api) |

## Çalışma Zamanı

| Bu rehberde | Resmi kaynak |
|---|---|
| [ToolNode & Hazır Ajanlar](orta-seviye/tool-node.md) | [Use the graph API](https://docs.langchain.com/oss/python/langgraph/use-graph-api) |
| [Checkpointer & Kalıcılık](orta-seviye/checkpointer.md) | [Persistence](https://docs.langchain.com/oss/python/langgraph/persistence), [Checkpointers](https://docs.langchain.com/oss/python/langgraph/checkpointers) |
| [Streaming](orta-seviye/streaming.md) | [Streaming](https://docs.langchain.com/oss/python/langgraph/streaming), [Event streaming](https://docs.langchain.com/oss/python/langgraph/event-streaming) |
| [Human-in-the-loop](orta-seviye/human-in-the-loop.md) | [Interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts) |
| [Subgraph & Store](orta-seviye/subgraph-store.md) | [Use subgraphs](https://docs.langchain.com/oss/python/langgraph/use-subgraphs), [Stores](https://docs.langchain.com/oss/python/langgraph/stores), [Memory](https://docs.langchain.com/oss/python/langgraph/add-memory) |
| [Test Yazma](orta-seviye/testing.md) | [Test](https://docs.langchain.com/oss/python/langgraph/test) |

## Mimari Desenler & Production

| Bu rehberde | Resmi kaynak |
|---|---|
| [State Tasarımı](ileri-seviye/state-tasarimi.md) | Resmi dokümanda ayrı bir sayfa yok — [Graph API overview](https://docs.langchain.com/oss/python/langgraph/graph-api) içindeki state bölümüne dayanır, gerisi bu rehberin kendi pratik önerileridir |
| [Time Travel & Debugging](ileri-seviye/time-travel.md) | [Use time-travel](https://docs.langchain.com/oss/python/langgraph/use-time-travel) |
| [Production & Deployment](ileri-seviye/production.md) | [Deployment](https://docs.langchain.com/oss/python/langgraph/deploy), [Fault-tolerance](https://docs.langchain.com/oss/python/langgraph/fault-tolerance) |
| [Ajan Değerlendirme](ileri-seviye/degerlendirme.md) | Resmi LangGraph dokümanında ayrı bir sayfa yok — LangSmith/Langfuse'un evaluation özellikleriyle ilişkilidir (ayrı rehber planlanıyor) |
| [Ajan Güvenliği](ileri-seviye/guvenlik.md) | Resmi LangGraph dokümanında ayrı bir sayfa yok — genel LLM/agent güvenlik pratiklerine (OWASP LLM Top 10 gibi kaynaklara) dayanır |
| [langgraph.json & CLI](ileri-seviye/deployment-cli.md) | [Run a local server](https://docs.langchain.com/oss/python/langgraph/local-server), [LangSmith Studio](https://docs.langchain.com/oss/python/langgraph/studio) |
| [Proje Yapısı](ileri-seviye/proje-yapisi.md) | [Application structure](https://docs.langchain.com/oss/python/langgraph/application-structure) |
| [Hata Referansı](referans/hata-referansi.md) | Resmi hata sayfaları (`docs.langchain.com/oss/python/langgraph/graph-recursion-limit` ve benzerleri) |

## Kapsam dışı bıraktığımız konular

Aşağıdaki resmi konular, niş kullanım alanları olduğu ya da bu rehberin "Türkçe, uçtan uca öğrenme" amacının dışında kaldığı için şimdilik eklenmedi:

- **Agent Chat UI** — hazır sohbet arayüzü bileşeni
- **Custom stream channels / Frontend entegrasyonu** — LangGraph'ı özel bir frontend'e bağlama detayları
- **LangSmith Observability entegrasyonu** — bu rehberin kapsamı LangGraph ile sınırlı; LangSmith kendi başına ayrı bir rehber olarak planlanıyor
- **Case studies, Changelog** — içerik değil, pazarlama/versiyon takibi amaçlı sayfalar

---

Resmi dokümantasyonun tamamı: [docs.langchain.com/oss/python/langgraph](https://docs.langchain.com/oss/python/langgraph/overview)
