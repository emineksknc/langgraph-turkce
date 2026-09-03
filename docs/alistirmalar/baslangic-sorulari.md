# Başlangıç Soruları

<span class="badge badge-baslangic">🟢 BAŞLANGIÇ</span>

Aşağıdaki sorular [Kavramlar](../baslangic/temel-kavramlar.md) ve [Koşullu Kenarlar](../baslangic/kosullu-kenarlar.md) sayfalarındaki konuları pekiştirmek içindir. Önce kendi cevabını düşün, sonra kutuya tıklayıp kontrol et.

??? question "1. LangChain Expression Language (LCEL) zinciri (`prompt | llm | parser`) ile LangGraph'ı ne zaman tercih edersin?"
    LCEL zincirleri **doğrusaldır** — A'dan B'ye, B'den C'ye gider. Döngü (bir aracı tekrar tekrar çağırmak), koşullu dallanma (duruma göre farklı yola sapmak) ya da kalıcı state (kesintiden devam edebilme) gerekiyorsa LangGraph kullanılır. Basit, tek yönlü bir "girdi → çıktı" akışı için LCEL yeterlidir.

??? question "2. State neden düz bir sözlük (dict) yerine `TypedDict` ile tanımlanır?"
    `TypedDict`, state'in hangi alanları içermesi gerektiğini ve bu alanların tiplerini açıkça tanımlar. Bu sayede hem IDE'den otomatik tamamlama/tip kontrolü alırsınız hem de grafı okuyan başka biri state'in yapısını kod içinden anlayabilir. Düz dict de teknik olarak çalışır ama okunabilirlik ve hata yakalama açısından `TypedDict` (ya da Pydantic modeli) önerilir.

??? question "3. `Annotated[list, add_messages]` ne işe yarar? Bunu kullanmazsanız ne olur?"
    Bu bir **reducer** tanımıdır. Reducer olmadan, bir node state alanına yeni bir değer döndürdüğünde bu değer **eskisinin üzerine yazılır**. `add_messages` reducer'ı sayesinde yeni mesajlar listeye **eklenir** (üzerine yazılmaz) — mesaj geçmişini korumak için şarttır.

??? question "4. Bir node fonksiyonu state'in tamamını mı yoksa sadece değişen kısmı mı döndürmelidir?"
    Sadece **değişen/eklenecek kısmı** döndürmelidir. Örneğin `return {"messages": [yanit]}` — bu, sadece `messages` alanına yeni bir eleman ekler; state'in diğer alanlarına dokunmaz. LangGraph bu kısmi güncellemeyi otomatik olarak mevcut state ile birleştirir (reducer varsa reducer mantığıyla, yoksa üzerine yazarak).

??? question "5. `START` ve `END` düğümleri normal node'lardan farkı nedir?"
    `START` ve `END`, LangGraph'ın kendi tanımladığı özel düğümlerdir; bir fonksiyon içermezler. `START`, grafın giriş noktasını (hangi node'la başlayacağını), `END` ise grafın sona erdiği noktayı belirtir. Her graf en az bir `START` kenarı içermelidir, yoksa graf nereden başlayacağını bilemez.

??? question "6. Koşullu kenar (`add_conditional_edges`) ile sabit kenar (`add_edge`) arasındaki fark nedir?"
    Sabit kenar, bir node'dan her zaman **aynı** sıradaki node'a gider. Koşullu kenar ise bir karar fonksiyonu çalıştırır ve bu fonksiyonun döndürdüğü değere göre **farklı** node'lara gidebilir — örneğin modelin bir araç çağırıp çağırmadığına bakarak "tools" node'una mı yoksa `END`'e mi gidileceğine karar verir.

??? question "7. `add_conditional_edges`'e `path_map` vermezseniz ne risk alırsınız?"
    Karar fonksiyonunuzun döndürdüğü string'in, bir sonraki node'un adıyla **birebir** eşleşmesi gerekir. `path_map` olmadan bir yazım hatası (örneğin `"arac"` yerine `"araç"` yazmak) derleme sırasında ya da çalışma zamanında anlaşılması güç hatalara yol açabilir. `path_map` vererek bu eşlemeyi açıkça yazmak hata ayıklamayı kolaylaştırır.

??? question "8. Bir grafı çalıştırılabilir hale getiren adım hangisidir?"
    `graph.compile()` çağrısı. Bu adımdan önce graf sadece bir tanımdır (node'lar ve kenarlar eklenmiş ama henüz "derlenmemiş" haldedir); `compile()` sonrası dönen `app` nesnesi üzerinden `invoke`, `stream`, `batch` gibi metodlar çağrılabilir.

??? question "9. En basit bir chatbot grafı (mesaj geçmişi olan ama araç kullanmayan) minimum kaç node içerir?"
    Tek bir node yeterlidir: `chatbot` node'u, modeli çağırıp cevabı state'e ekler. Graf `START → chatbot → END` şeklinde kurulur. Araç çağırma, döngü gibi ihtiyaçlar ortaya çıktıkça (bkz. [ToolNode](../orta-seviye/tool-node.md)) node sayısı artar.

??? question "10. Aynı `thread_id` ile yapılan iki ayrı `invoke()` çağrısı arasında ne olur? (Checkpointer varsa)"
    Checkpointer tanımlıysa, ikinci çağrı ilk çağrının bıraktığı state'in üzerinden devam eder — yani model önceki mesajları "hatırlar". Checkpointer yoksa her `invoke()` çağrısı sıfırdan başlar, önceki konuşmadan hiçbir iz kalmaz. Detaylar için: [Checkpointer & Kalıcılık](../orta-seviye/checkpointer.md).

---

Hazır mısın? Sıradaki seviye: [Orta Seviye Soruları](orta-seviye-sorulari.md)
