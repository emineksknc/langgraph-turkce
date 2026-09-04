# Sözlük — Terimler

Bu sayfada rehber boyunca geçen terimler, orijinal İngilizce karşılıklarıyla birlikte kısaca açıklanıyor. Bir terimi sayfa içinde ilk gördüğünde de yanında kısa bir tanım bulacaksın — burası hepsinin toplu, aranabilir hâli.

## Temel Kavramlar

**State (durum)**
: Grafın tüm düğümleri arasında akan, paylaşılan veri yapısı. Konuşma geçmişi, kullanıcı bilgisi gibi verileri taşır. Bkz. [Temel Kavramlar](baslangic/temel-kavramlar.md).

**Node (düğüm)**
: State'i alıp state'e eklenecek kısmi güncellemeyi döndüren bir Python fonksiyonu. Grafın "iş yapan" birimidir.

**Edge (kenar)**
: İki düğüm arasındaki bağlantı; akışın hangi sırayla ilerleyeceğini belirler.

**Reducer (indirgeyici)**
: Bir state alanına yeni değer geldiğinde bu değerin öncekiyle nasıl birleştirileceğini belirleyen fonksiyon. Reducer olmadan yeni değer eskisinin üzerine yazılır; `add_messages` gibi bir reducer ile eklenir.

**Conditional edge (koşullu kenar) / Branching (dallanma)**
: Akışın bir sonraki adımının, state'e bakılarak çalışma zamanında karar verildiği kenar tipi. "Dallanma" terimi, akışın burada birden fazla olası yola ayrılabilmesini ifade eder.

**Compile (derleme)**
: `graph.compile()` çağrısı — tanımlanan node/edge yapısını çalıştırılabilir bir uygulamaya (`app`) dönüştürür.

**Invoke (çağırma)**
: `app.invoke(...)` — grafı bir kez, baştan sona çalıştıran temel metod.

**Workflow (iş akışı)**
: Adımların sırası kod tarafından önceden belirlenen sistem türü — LLM karar verse bile hangi adımların var olduğu sabittir. Bkz. [Workflows vs Agents](baslangic/workflows-vs-agents.md).

## API Stilleri & Çalışma Modeli

**Graph API**
: `StateGraph`, `add_node`, `add_edge` ile grafı açıkça node/kenar olarak tanımladığınız, bu rehberde varsayılan olarak kullanılan yazım şekli.

**Functional API**
: `@entrypoint` ve `@task` dekoratörleriyle, grafı normal Python fonksiyonlarına yakın bir sözdizimiyle yazmanızı sağlayan alternatif LangGraph API'si. Aynı checkpointer/human-in-the-loop altyapısını kullanır. Bkz. [Functional API](orta-seviye/functional-api.md).

**@task**
: Functional API'de tek bir işlem birimini işaretleyen dekoratör — Graph API'deki bir node'a karşılık gelir.

**@entrypoint**
: Functional API'de tüm akışın giriş noktasını işaretleyen dekoratör — Graph API'deki `compile()` edilmiş `app`'e karşılık gelir.

**Durable execution (dayanıklı çalıştırma)**
: Bir çalıştırmanın, çökme/yeniden başlatma/geçici hata gibi kesintilere rağmen kaldığı yerden devam edebilmesi. Checkpointer, retry policy ve human-in-the-loop birlikte bu özelliği sağlar. Bkz. [Production & Deployment](ileri-seviye/production.md).

## Kalıcılık & Hafıza

**Checkpointer (kontrol noktası kaydedici)**
: Grafın her adımdan sonraki state'ini kalıcı olarak kaydeden bileşen. Kesintiden sonra devam etmeyi ve [time-travel](ileri-seviye/time-travel.md)'i mümkün kılar.

**Short-term memory (kısa süreli hafıza)**
: Tek bir thread'e (konuşmaya) özel hafıza — checkpointer tarafından tutulur. "Bu konuşmada ne konuştuk" sorusunun cevabıdır.

**Long-term memory (uzun süreli hafıza)**
: Thread'ler arası, kalıcı hafıza — `Store` tarafından tutulur. "Bu kullanıcı hakkında ne biliyorum" sorusunun cevabıdır. Bkz. [Subgraph & Store](orta-seviye/subgraph-store.md).

**Thread (iş parçacığı / konuşma dizisi)**
: Tek bir konuşmayı/oturumu tanımlayan kimlik (`thread_id`). Aynı thread'e ait çağrılar aynı state üzerinden devam eder. (Programlamadaki "iş parçacığı" kavramıyla karıştırılmamalı — burada "tek bir konuşma dizisi" anlamındadır.)

**Store (depo)**
: Thread'ler arası kalıcı, aranabilir hafıza. Kullanıcı tercihleri gibi tüm konuşmalarda hatırlanması gereken bilgiler için kullanılır.

**Semantic search (anlamsal arama)**
: Anahtar kelime eşleşmesi yerine, embedding (gömme vektörü) kullanarak anlam benzerliğine göre arama yapma yöntemi.

**Embedding (gömme vektörü)**
: Bir metni, anlamını sayısal olarak temsil eden bir vektöre (sayı dizisine) dönüştürme işlemi; anlamsal arama bu vektörler arası benzerlik hesabına dayanır.

## Araçlar & Ajanlar

**Tool (araç)**
: Modelin çağırabileceği, dış dünyayla etkileşim kuran bir fonksiyon (ör. hava durumu sorgulama, veritabanı okuma).

**Tool calling (araç çağırma)**
: Modelin, verilen görevi yerine getirmek için bir veya daha fazla aracı çağırmaya karar vermesi.

**ToolNode**
: LangGraph'ın hazır sunduğu, araç çağrılarını otomatik çalıştıran node. Elle yazmaya gerek bırakmaz.

**ReAct (Reason + Act — "akıl yürüt ve eyle")**
: "Düşün → araç çağır → sonucu değerlendir → gerekirse tekrar düşün" döngüsüne dayanan klasik ajan deseni. `create_react_agent` bu deseni hazır sağlar.

**Agent (ajan)**
: Bir görevi tamamlamak için kendi kendine karar verip araç çağırabilen LLM tabanlı sistem.

## Multi-Agent (Çoklu Ajan) Mimarileri

**Supervisor (yönetici/denetleyici)**
: Görevi hangi uzman ajanın üstleneceğine karar veren merkezi bir düğüm.

**Swarm (sürü)**
: Merkezi bir yönetici olmadan ajanların birbirine doğrudan görev devrettiği mimari.

**Handoff (devir)**
: Bir ajanın, görevi başka bir ajana devretme kararı ve işlemi.

**Subgraph (alt graf)**
: Başka bir grafın içine node olarak gömülen, bağımsız çalışabilen küçük bir graf.

## Kontrol Akışı & İnsan Etkileşimi

**Command (komut)**
: Bir düğümün hem state güncellemesini hem de bir sonraki düğümü tek seferde belirtmesini sağlayan nesne.

**Send (gönder) / Map-reduce**
: Çalışma zamanında bilinmeyen sayıda paralel görev başlatıp sonuçları birleştirme deseni. "Map" (dağıtma) ve "reduce" (birleştirme) klasik veri işleme terimleridir.

**Human-in-the-loop (döngüde insan)**
: Kritik adımlarda otomasyonu durdurup insan onayı bekleyen tasarım deseni.

**Interrupt (kesme/durma)**
: Grafın belirli bir noktada çalışmayı durdurup insan girdisi beklemesi.

**NodeInterrupt**
: `interrupt()`'tan farklı olarak, bir düğümün içinden **koşullu olarak** fırlatılan, veri bazlı durma istisnası (ör. "tutar eşiği aşıldıysa dur").

**Time-travel (zamanda geri gitme)**
: Grafın geçmiş bir state'ine dönüp oradan farklı bir dal deneme yeteneği.

## Yapılandırma & Üretim

**Config injection (yapılandırma enjeksiyonu)**
: Bir düğüme, state'in parçası olmayan ama çağrıya özel bilgiyi (kullanıcı kimliği, model seçimi gibi) `RunnableConfig` üzerinden geçirme yöntemi.

**RunnableConfig**
: LangChain/LangGraph'ta çalışma zamanı parametrelerini taşıyan standart yapılandırma nesnesi.

**Retry policy (yeniden deneme politikası)**
: Bir düğümün, geçici hatalarda (ağ, rate limit) otomatik olarak kaç kez yeniden deneneceğini belirleyen kural.

**Recursion limit (özyineleme/döngü üst sınırı)**
: Döngü içeren bir grafın en fazla kaç adım çalışabileceğine dair güvenlik sınırı.

**CLI (Command Line Interface — komut satırı arayüzü)**
: `langgraph` komutuyla kullanılan, projeyi yerelde çalıştırma/derleme/deploy etme aracı.

**Manifest (bildirim dosyası)**
: `langgraph.json` — projenin hangi graf(lar)ı, bağımlılıkları ve ortam değişkenlerini kullandığını tanımlayan yapılandırma dosyası.

**Application structure (proje yapısı)**
: Node, state, tool gibi bileşenlerin ayrı dosya/klasörlere organize edildiği, büyüyen bir LangGraph projesi için önerilen düzen. Bkz. [Proje Yapısı](ileri-seviye/proje-yapisi.md).

## Test & Değerlendirme

**Mock / Fake (sahte nesne)**
: Gerçek bir bileşenin (ör. LLM) yerine geçen, test sırasında öngörülebilir/ücretsiz sonuç döndüren yapay sürüm. `FakeListChatModel` buna bir örnektir.

**LLM-as-judge (yargıç olarak LLM)**
: Bir LLM'in, başka bir LLM'in ürettiği cevabın kalitesini otomatik olarak puanlaması yöntemi.

**Regression test (regresyon testi)**
: Yapılan bir değişikliğin, daha önce doğru çalışan bir davranışı bozup bozmadığını kontrol eden test.

---

Bir terimi bulamadıysan veya eksik/yanlış bir tanım gördüysen, [GitHub reposundan](https://github.com/emineksknc/langgraph-turkce) bir issue/PR açabilirsin.
