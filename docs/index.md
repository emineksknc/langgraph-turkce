# LangGraph Türkçe Rehber

Bu site, [LangGraph](https://github.com/langchain-ai/langgraph) kütüphanesini **sıfırdan senior seviyeye** kadar öğrenmek isteyenler için hazırlanmış, Türkçe kapsamlı bir kaynaktır.

!!! info "Neden bu site?"
    LangGraph'ın resmi dokümantasyonu İngilizce ve düzenli olarak değişiyor. Bu site; kavramları Türkçe, örneklerle ve kademeli bir öğrenme sırasıyla (başlangıç → orta → ileri) anlatmayı hedefliyor. Bağımsız bir öğretim kaynağıdır, LangChain Inc. ile bağlantılı değildir.

## Nereden başlamalıyım?

<div class="grid cards" markdown>

- :material-flag-checkered: **Yeni misin?**
  [Kurulum](baslangic/kurulum.md) sayfasından başla, ardından [Temel Kavramlar](baslangic/temel-kavramlar.md)'a geç.

- :material-graph: **LangChain biliyorsun, LangGraph'a mı geçiyorsun?**
  Doğrudan [Temel Kavramlar](baslangic/temel-kavramlar.md) ve [ToolNode & Hazır Ajanlar](orta-seviye/tool-node.md) sayfalarına bak.

- :material-account-hard-hat: **Production'a mı hazırlanıyorsun?**
  [Checkpointer & Kalıcılık](orta-seviye/checkpointer.md) ve [Production & Deployment](ileri-seviye/production.md) sayfalarını incele.

</div>

## Sıfırdan senior'a okuma sırası

Yan menü **konu bazlı** düzenlendi (referans olarak kullanması kolay olsun diye), ama sıfırdan başlıyorsan aşağıdaki sırayla okumanı öneririz. Her sayfanın başındaki 🟢/🟡/🔴 rozeti seviyesini gösterir.

1. 🟢 [Kurulum](baslangic/kurulum.md)
2. 🟢 [State, Node, Edge](baslangic/temel-kavramlar.md)
3. 🟢 [İlk Grafını Kur](baslangic/ilk-graf.md)
4. 🟢 [Koşullu Kenarlar](baslangic/kosullu-kenarlar.md)
5. 🟡 [ToolNode & Hazır Ajanlar](orta-seviye/tool-node.md)
6. 🟡 [Checkpointer & Kalıcılık](orta-seviye/checkpointer.md)
7. 🟡 [Streaming](orta-seviye/streaming.md)
8. 🟡 [Human-in-the-loop](orta-seviye/human-in-the-loop.md)
9. 🟡 [Subgraph & Store](orta-seviye/subgraph-store.md)
10. 🔴 [Command & Send API](ileri-seviye/send-command.md)
11. 🔴 [Multi-Agent Mimarileri](ileri-seviye/multi-agent.md)
12. 🔴 [Time Travel & Debugging](ileri-seviye/time-travel.md)
13. 🔴 [Production & Deployment](ileri-seviye/production.md)

!!! tip "Öğrendiklerini test et"
    Her seviye için açılır-kapanır soru-cevap setleri hazırlandı — kendi kendine test edebilirsin: [Başlangıç](alistirmalar/baslangic-sorulari.md) · [Orta Seviye](alistirmalar/orta-seviye-sorulari.md) · [İleri Seviye](alistirmalar/ileri-seviye-sorulari.md)

## İndirilebilir Cheatsheet'ler (PDF)

Her seviye için, çevrimdışı da kullanabileceğin 2 sayfalık özet referans kartları:

<div class="grid cards" markdown>

- :material-file-pdf-box: **🟢 Başlangıç Cheatsheet**
  State, Node, Edge, ilk graf, koşullu kenarlar.
  [PDF indir](assets/cheatsheets/langgraph-baslangic-cheatsheet.pdf){ .md-button }

- :material-file-pdf-box: **🟡 Orta Seviye Cheatsheet**
  ToolNode, checkpointer, streaming, human-in-the-loop, subgraph/store.
  [PDF indir](assets/cheatsheets/langgraph-orta-seviye-cheatsheet.pdf){ .md-button }

- :material-file-pdf-box: **🔴 İleri Seviye Cheatsheet**
  Command &amp; Send API, multi-agent, time-travel, production.
  [PDF indir](assets/cheatsheets/langgraph-ileri-seviye-cheatsheet.pdf){ .md-button }

</div>

## Katkıda bulunmak ister misin?

Bu site açık kaynak. Bir hata bulursan, eksik bir konu görürsen ya da kendi deneyimini eklemek istersen [GitHub reposundan](https://github.com/KULLANICI_ADIN/langgraph-turkce) pull request açabilirsin.

---

*LangGraph, LangChain Inc. tarafından geliştirilen, [MIT lisanslı](https://github.com/langchain-ai/langgraph/blob/main/LICENSE) açık kaynaklı bir kütüphanedir.*
