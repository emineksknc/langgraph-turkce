# Orta Seviye Soruları

<span class="badge badge-orta">🟡 ORTA SEVİYE</span>

Bu sorular [Çalışma Zamanı](../orta-seviye/tool-node.md) bölümündeki konuları (ToolNode, checkpointer, streaming, human-in-the-loop, subgraph/store) test eder.

??? question "1. `tools_condition` fonksiyonu tam olarak neye karar verir?"
    Son mesajda (`AIMessage`) `tool_calls` alanı doluysa `"tools"` node'una, boşsa `END`'e yönlendirir. Aslında bir önceki seviyede elle yazdığımız `yon_bul` fonksiyonunun LangGraph tarafından sağlanan hazır hâlidir — standart araç-çağırma döngüsü için tekrar tekrar aynı kodu yazmamak amacıyla kullanılır.

??? question "2. `create_react_agent` yerine ne zaman StateGraph'ı elle kurmalısınız?"
    `create_react_agent`, standart "düşün → araç çağır → cevapla" döngüsü için idealdir ve hızlıdır. Ama özel bir akış gerekiyorsa — birden fazla ajan arasında yönlendirme, insan onayı bekleyen adımlar, koşullu dallanma, paralel işleme gibi — StateGraph'ı elle kurmak çok daha fazla kontrol sağlar. Kısacası: standart görev → hazır fonksiyon; özel/karmaşık akış → elle graf.

??? question "3. `MemorySaver` ile `PostgresSaver` arasındaki temel fark nedir, hangisi ne zaman kullanılır?"
    `MemorySaver` state'i sadece process belleğinde tutar — süreç yeniden başladığında (deploy, crash, restart) tüm veri kaybolur; sadece **geliştirme/test** için uygundur. `PostgresSaver` (veya `SqliteSaver`) state'i kalıcı bir veritabanına yazar; **production**'da kullanılması gereken seçenektir çünkü süreç yeniden başlasa bile konuşma geçmişi korunur.

??? question "4. `thread_id` neyi belirler, farklı kullanıcılar için nasıl kullanılmalı?"
    `thread_id`, checkpointer'ın hangi konuşmaya ait state'i okuyup yazacağını belirler. Her kullanıcı/konuşma için **farklı** bir `thread_id` kullanılmalı (ör. `f"kullanici-{user_id}"` veya bir session UUID'si) — aynı `thread_id`'yi paylaşan iki farklı kullanıcı, birbirinin konuşma geçmişini görür/karıştırır.

??? question "5. `stream_mode=\"values\"`, `\"updates\"` ve `\"messages\"` arasındaki fark nedir?"
    - `values`: her adımdan sonra **state'in tamamını** döner — debug için kullanışlı.
    - `updates`: sadece **o adımda değişen kısmı** döner — hangi node'un ne yaptığını görmek için.
    - `messages`: LLM'den gelen **token akışını** döner — kullanıcıya canlı "yazıyor..." efekti göstermek (chat arayüzü) için kullanılır.

??? question "6. `astream_events` normal `stream()`'den farkı nedir, ne zaman tercih edilir?"
    `stream()` sadece node/token bazlı çıktı verir. `astream_events`, grafın içindeki **her türlü olayı** (retriever başladı/bitti, tool çağrıldı, LLM token'ı geldi vb.) ayrı ayrı yakalar. UI'da "araç çağrılıyor...", "belgeler aranıyor..." gibi ara durum göstergeleri yapmak istediğinizde `astream_events` kullanılır.

??? question "7. `interrupt_before=[\"tools\"]` ile derlenen bir graf, `tools` node'undan önce durduğunda nasıl devam ettirilir?"
    Aynı `config` (aynı `thread_id`) ile `app.invoke(None, config)` çağrılır. `None` girdisi, "state'i değiştirmeden kaldığın yerden devam et" anlamına gelir. Devam etmeden önce `app.update_state(config, {...})` ile state'i değiştirmek de mümkündür (ör. kullanıcı bir tutarı düzelttiyse).

??? question "8. Checkpointer ile Store arasındaki temel fark nedir?"
    Checkpointer, **tek bir thread'e** (konuşmaya) özel, o thread'in adım adım geçmişini tutan kısa süreli hafızadır. Store ise **thread'ler arası**, kalıcı ve aranabilir bir hafızadır — örneğin bir kullanıcının farklı konuşmalarda hatırlanması gereken tercihleri Store'da tutulur, her thread'de sıfırdan sorulmaz.

??? question "9. Subgraph kullanmanın en somut faydası nedir?"
    Büyük bir grafı mantıksal parçalara bölerek **modüler** hale getirir — her parça bağımsız geliştirilip test edilebilir (tıpkı bir fonksiyonu başka bir fonksiyonun içinde kullanmak gibi). Ekip içinde farklı kişiler farklı subgraph'lar üzerinde paralel çalışabilir.

??? question "10. Bir node fonksiyonunun içinden Store'a nasıl erişilir?"
    Node fonksiyonuna `store` parametresi eklenir (`def node_fn(state, *, store): ...`) ve graf `compile(checkpointer=..., store=store)` ile derlenirken store verilir. LangGraph, node çağrılırken store'u otomatik olarak enjekte eder.

---

Bir önceki seviyeye dönmek için: [Başlangıç Soruları](baslangic-sorulari.md) · Sıradaki seviye: [İleri Seviye Soruları](ileri-seviye-sorulari.md)
