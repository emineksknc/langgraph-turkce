# Test Yazma

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

LangGraph uygulamaları da normal Python kodu gibi test edilebilir — ama LLM çağrılarının maliyeti ve belirsizliği (aynı prompt'a her seferinde aynı cevabı vermeyebilir) yüzünden birkaç ek desen gerekir.

## Katman 1 — Node fonksiyonlarını saf fonksiyon gibi test etmek

Node fonksiyonlarınız state alıp state döndüren saf fonksiyonlardır — LLM çağrısı içermeyen node'ları normal `pytest` ile test edebilirsiniz.

```python
def yon_bul(state: State) -> str:
    return "arac" if state["messages"][-1].tool_calls else "__end__"

def test_yon_bul_arac_cagrisi_varsa():
    sahte_state = {"messages": [AIMessage(content="", tool_calls=[{"name": "hava", "args": {}, "id": "1"}])]}
    assert yon_bul(sahte_state) == "arac"

def test_yon_bul_arac_cagrisi_yoksa():
    sahte_state = {"messages": [AIMessage(content="cevap")]}
    assert yon_bul(sahte_state) == "__end__"
```

## Katman 2 — LLM çağıran node'ları sahte (fake) modelle test etmek

Gerçek API'ye para ödemeden zincir/graf **mantığını** test etmek için `FakeListChatModel` gibi **mock/fake** (sahte nesne — gerçek bileşenin yerine geçen, öngörülebilir sonuç döndüren test aracı) modeller kullanılır — böylece "model şu cevabı verdiğinde graf doğru node'a gidiyor mu?" sorusunu deterministik şekilde test edebilirsiniz.

```python
from langchain_core.language_models.fake_chat_models import FakeListChatModel

sahte_llm = FakeListChatModel(responses=["İstanbul'da hava güneşli."])

def test_chatbot_node_cevap_uretiyor():
    state = {"messages": [("user", "hava nasıl?")]}
    sonuc = chatbot_with_llm(state, llm=sahte_llm)
    assert "güneşli" in sonuc["messages"][-1].content
```

## Katman 3 — Grafın tamamını `invoke()` ile uçtan uca test etmek

```python
def test_grafin_tamami_dogru_akista_calisiyor():
    app = graph.compile(checkpointer=MemorySaver())
    config = {"configurable": {"thread_id": "test-1"}}

    sonuc = app.invoke({"messages": [("user", "500 TL öde")]}, config)

    # tools node'undan geçtiğini, doğru sırayla ilerlediğini kontrol et
    assert sonuc["messages"][-1].content is not None
```

`MemorySaver` testlerde idealdir — her test fonksiyonunda temiz bir state ile başlarsınız, dış bir veritabanına ihtiyaç duymazsınız.

## Katman 4 — Kalite/regresyon testleri (LLM-as-judge)

Node mantığının doğru çalıştığını test etmek başka, **cevabın kalitesinin** production'a çıkacak seviyede olduğunu test etmek başka bir şeydir. Bu ikinci tür teste **regression test** (regresyon testi — bir değişikliğin önceden doğru çalışan bir davranışı bozup bozmadığını kontrol eden test) denir. Bunun için sabit bir soru-cevap seti üzerinde otomatik değerlendirme çalıştırılır; değerlendirmeyi bir LLM'e yaptırma yöntemine **LLM-as-judge** (yargıç olarak LLM) denir — bkz. LangSmith `evaluate()` fonksiyonu (LangSmith bölümü yakında bu rehbere eklenecek).

```python
def test_dogru_cevap_orani_esik_ustunde(dataset):
    dogru_sayisi = 0
    for ornek in dataset:
        cevap = app.invoke({"messages": [("user", ornek["soru"])]}, config)
        if ornek["beklenen"] in cevap["messages"][-1].content:
            dogru_sayisi += 1
    assert dogru_sayisi / len(dataset) >= 0.9  # %90 doğruluk eşiği
```

!!! tip "CI/CD'ye entegre et"
    Katman 1-3'teki testler hızlı ve ücretsizdir — her pull request'te çalıştırılabilir. Katman 4 (gerçek LLM çağrısı içerir) daha yavaş ve maliyetlidir — genelde `main` branch'ine merge öncesi ya da günlük olarak çalıştırılır.

---

Sıradaki bölüm: [Mimari Desenler — Command & Send API](../ileri-seviye/send-command.md).
