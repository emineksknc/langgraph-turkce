# Time Travel & Debugging

<span class="badge badge-ileri">🔴 İLERİ / SENIOR</span>

Checkpointer sayesinde LangGraph, bir thread'in **tüm state geçmişini** saklar. Bu yeteneğe **time-travel** (zamanda geri gitme) denir ve hem hata ayıklama hem de "farklı bir karar verilseydi ne olurdu" analizleri için güçlü bir araçtır.

## State geçmişini listeleme

```python
for snapshot in app.get_state_history(config):
    print(snapshot.values, "| sıradaki node:", snapshot.next)
```

Her `snapshot`, o ana kadarki tam state'i ve o noktadan sonra çalışacak node'u içerir.

## Belirli bir noktaya dönüp devam etme

```python
# Geçmişten belirli bir checkpoint'i seç
gecmis = list(app.get_state_history(config))
hedef_checkpoint = gecmis[3]  # 3 adım öncesi

# O noktadan farklı bir girdiyle devam et
app.invoke(None, {"configurable": {
    "thread_id": config["configurable"]["thread_id"],
    "checkpoint_id": hedef_checkpoint.config["configurable"]["checkpoint_id"],
}})
```

Bu, örneğin bir ajanın 5. adımda yanlış bir araç seçtiğini fark ettiğinizde, 4. adıma dönüp farklı bir prompt/state ile tekrar denemenizi sağlar — tüm konuşmayı baştan çalıştırmanıza gerek kalmaz.

## State'i manuel düzenleme

```python
app.update_state(
    config,
    {"messages": [("assistant", "düzeltilmiş cevap")]},
    as_node="chatbot",
)
```

`as_node`, güncellemenin hangi node tarafından yapılmış gibi görüneceğini belirtir — grafın sıradaki adımı doğru hesaplaması için önemlidir.

## Pratik debug ipuçları

- **`recursion_limit` hatası alıyorsanız**: muhtemelen bir döngü `END`'e ulaşamıyor — koşullu kenar mantığınızı kontrol edin.
- **State beklenmedik şekilde sıfırlanıyorsa**: reducer eksik olabilir (bkz. [Temel Kavramlar](../baslangic/temel-kavramlar.md)).
- **Karmaşık multi-agent sistemlerde "hangi ajan ne karar verdi" takibi için**: LangSmith veya Langfuse trace'lerini node bazında incelemek, `get_state_history`'den çok daha pratiktir (bu araçlar için ayrı bir bölüm ileride eklenecek).

---

Sıradaki adım: [Production & Deployment](production.md).
