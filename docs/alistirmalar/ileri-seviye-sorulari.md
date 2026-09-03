# İleri Seviye Soruları

<span class="badge badge-ileri">🔴 İLERİ / SENIOR</span>

Bu sorular [Mimari Desenler](../ileri-seviye/send-command.md) ve [Production](../ileri-seviye/production.md) bölümlerindeki konuları test eder.

??? question "1. `Command` nesnesi, ayrı bir `add_conditional_edges` kullanmaya göre ne avantaj sağlar?"
    Normalde bir node sadece state günceller; sıradaki node'u ayrı bir routing fonksiyonu (`add_conditional_edges`) belirler. `Command(goto=..., update={...})`, **hem state'i güncellemeyi hem de sıradaki node'u** tek bir dönüş değerinde birleştirir — özellikle bir "supervisor" node'un kararını ifade ederken ayrı bir routing fonksiyonu yazmaktan kurtarır, kod daha okunabilir olur.

??? question "2. `Send` API'nin çözdüğü problem nedir? Sabit sayıda `add_edge` ile neden çözülemez?"
    `Send`, **çalışma zamanında kaç paralel dalın açılacağı belli olmadığında** kullanılır — örneğin kullanıcının yüklediği N adet belgeyi paralel özetlemek. Sabit kenarlarla N önceden bilinmediği için düğüm sayısı statik olarak tanımlanamaz; `Send("ozetle", {...})`, her belge için node'un ayrı bir kopyasını dinamik olarak oluşturur.

??? question "3. Supervisor mimarisi ile swarm mimarisi arasındaki temel fark nedir? Hangi durumda hangisi tercih edilir?"
    Supervisor'da merkezi bir yönetici node, hangi uzman ajanın görevi üstleneceğine karar verir — tüm kararlar tek yerden geçtiği için **denetlenebilirlik yüksektir**. Swarm'da merkezi yönetici yok; ajanlar birbirine doğrudan devir (handoff) yapar — daha **esnektir** ama takip etmesi/debug etmesi zordur. Kurumsal/regüle iş akışları için supervisor, araştırma/keşif odaklı sistemler için swarm daha uygundur.

??? question "4. Hiyerarşik multi-agent mimarisi hangi temel yapı taşıyla kurulur?"
    Subgraph mekanizmasıyla. Bir supervisor'ın kendisi, başka bir üst supervisor'a bağlı bir "takım" (subgraph) olabilir. Örneğin bir "Genel Müdür" supervisor, "İçerik Takımı" ve "Kod Takımı" adlı iki subgraph'ı yönetebilir; her subgraph kendi içinde ayrı bir supervisor + uzman ajanlar içerir.

??? question "5. `get_state_history()` neden faydalıdır, tipik bir kullanım senaryosu nedir?"
    Bir thread'in **tüm state geçmişini** (her checkpoint'i) listelemenizi sağlar. Tipik senaryo: bir ajanın 5. adımda yanlış bir karar verdiğini fark ettiğinizde, 4. adıma dönüp farklı bir state/girdiyle tekrar denemek — konuşmayı sıfırdan tekrar çalıştırmaya gerek kalmaz (time-travel debugging).

??? question "6. `update_state()`'e verilen `as_node` parametresi ne işe yarar?"
    Yapılan state güncellemesinin, **hangi node tarafından yapılmış gibi** görüneceğini belirtir. Bu, LangGraph'ın "sıradaki adım ne olmalı" hesaplamasını doğru yapabilmesi için önemlidir — çünkü graf, bir sonraki node'u genelde "son hangi node çalıştı" bilgisine göre belirler.

??? question "7. `RetryPolicy` hangi tür node'larda özellikle önemlidir, neden?"
    Harici bir API çağıran node'larda (LLM çağrısı, üçüncü parti servis, veritabanı sorgusu) önemlidir çünkü bu tür çağrılar **geçici hatalara** (rate limit, ağ zaman aşımı) açıktır. `RetryPolicy(max_attempts=3)` gibi bir tanım, geçici bir hatada tüm grafı çökertmek yerine otomatik olarak birkaç kez yeniden denemeyi sağlar.

??? question "8. `recursion_limit` koymadan döngü içeren bir graf çalıştırırsanız en kötü senaryoda ne olur?"
    Mantık hatası nedeniyle döngü hiç `END`'e ulaşmazsa graf **sonsuza kadar** (ya da sistem kaynakları tükenene kadar) çalışmaya devam eder — her turda LLM çağrısı yapılıyorsa bu token maliyetinin kontrolsüz artması anlamına gelir. `recursion_limit`, bu senaryoda grafı belirli bir adımdan sonra hata fırlatarak durdurur.

??? question "9. Production'da `MemorySaver` yerine kalıcı bir checkpointer kullanmak neden sadece 'iyi bir pratik' değil, kritik bir gereksinimdir?"
    Çünkü production ortamları (deploy, otomatik ölçekleme, crash sonrası restart) sürekli yeniden başlayan süreçlerdir. `MemorySaver` process belleğine bağlı olduğu için her yeniden başlatmada **tüm aktif kullanıcı konuşmaları sıfırlanır** — kullanıcı açısından "bot beni unuttu" deneyimi yaratır. Kalıcı bir backend (Postgres/Redis) bu veriyi süreç ömründen bağımsız hale getirir.

??? question "10. LangGraph Platform (managed) ile kendi FastAPI sarmalayıcınızı deploy etmek arasında nasıl karar verirsiniz?"
    Karar; altyapı yönetme isteği/kapasitesi, mevcut sistemlere entegrasyon ihtiyacı ve ölçek gereksinimlerine bağlıdır. Hızlı başlamak ve altyapı (checkpointer, ölçekleme, izleme) yönetmek istemiyorsanız **LangGraph Platform** uygun olur. Mevcut altyapınıza (kendi auth sisteminiz, özel middleware, mevcut Kubernetes kümesi) derin entegrasyon gerekiyorsa **kendi FastAPI sarmalayıcınız** ile tam kontrol elde edersiniz — ama checkpointer/ölçekleme/izleme sorumluluğu size kalır.

---

Bir önceki seviyeye dönmek için: [Orta Seviye Soruları](orta-seviye-sorulari.md) · Baştan başlamak için: [Başlangıç Soruları](baslangic-sorulari.md)
