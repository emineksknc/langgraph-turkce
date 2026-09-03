# Multi-Agent Mimarileri

<span class="badge badge-ileri">🔴 İLERİ / SENIOR</span>

Tek bir ajanın tüm araçlara/bağlama sahip olması hem prompt'u şişirir hem de modelin doğru karar verme olasılığını düşürür. Çözüm: görevi uzmanlaşmış ajanlara bölmek.

## Supervisor (yönetici) deseni

Merkezi bir "supervisor" node, görevi hangi uzman ajanın üstleneceğine karar verir. Denetlenebilirlik ve öngörülebilirlik önceliğiyse en yaygın tercihtir.

```python
def supervisor_node(state: State) -> Command[Literal["arastirmaci", "kodlayici", "__end__"]]:
    karar = llm.invoke([
        SystemMessage("Görevi 'arastirmaci', 'kodlayici' ajanlarından birine yönlendir, bitti ise 'FINISH' de."),
        *state["messages"],
    ])
    hedef = karar.content if karar.content != "FINISH" else "__end__"
    return Command(goto=hedef)

graph.add_node("supervisor", supervisor_node)
graph.add_node("arastirmaci", arastirmaci_agent)
graph.add_node("kodlayici", kodlayici_agent)
graph.add_edge("arastirmaci", "supervisor")  # iş bitince supervisor'a raporla
graph.add_edge("kodlayici", "supervisor")
graph.add_edge(START, "supervisor")
```

Hazır bir kütüphane de mevcuttur:

```python
from langgraph_supervisor import create_supervisor
from langgraph.prebuilt import create_react_agent

arastirma_ajani = create_react_agent(llm, tools=[web_arama], name="arastirmaci")
kod_ajani = create_react_agent(llm, tools=[kod_calistir], name="kodlayici")

app = create_supervisor([arastirma_ajani, kod_ajani], model=llm).compile()
```

## Swarm deseni

Merkezi bir yönetici yok — ajanlar birbirine doğrudan **devir (handoff)** yapar; her ajan "kime devredeyim" kararını kendisi verir (genelde bir "handoff" aracı çağırarak).

| | Supervisor | Swarm |
|---|---|---|
| Denetlenebilirlik | Yüksek (tüm kararlar tek yerden geçer) | Düşük — takip etmesi zor olabilir |
| Esneklik | Orta | Yüksek — ajanlar organik olarak işbirliği yapar |
| Debug kolaylığı | Kolay | Zor |
| Önerilen kullanım | Kurumsal/regüle iş akışları | Araştırma, keşif odaklı sistemler |

## Hiyerarşik (nested) mimari

Büyük sistemlerde bir supervisor'ın kendisi de başka bir üst supervisor'a bağlı bir "takım" olabilir — [subgraph](../orta-seviye/subgraph-store.md) mekanizmasıyla doğal olarak modellenir. Ör: "İçerik Takımı" (araştırmacı + yazar) ve "Kod Takımı" (mimar + geliştirici + test uzmanı), ikisi de "Genel Müdür" supervisor'a bağlı.

!!! tip "Mimari seçim kılavuzu"
    - Görev sınırları net ve denetim önemliyse → **supervisor**
    - Ajanlar arası organik işbirliği, keşif odaklı görevler → **swarm**
    - Sistem büyüdükçe → **hiyerarşik** (supervisor'lar arası supervisor)

---

Sıradaki adım: hataları ve kararları geriye dönük incelemek için [Time Travel & Debugging](time-travel.md).
