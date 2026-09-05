# Hata Referansı

Bu sayfa, LangGraph çalışırken karşılaşabileceğin en yaygın hataları ve çözümlerini listeler.

## GRAPH_RECURSION_LIMIT

**Ne zaman olur:** Grafın çalışma adımı sayısı, [`recursion_limit`](../ileri-seviye/production.md) değerini aştığında.

```
GraphRecursionError: Recursion limit of 25 reached without hitting a stop condition.
```

**Muhtemel neden:** Bir döngü (ör. [araç çağırma döngüsü](../orta-seviye/tool-node.md)) hiç `END`'e ulaşmıyor — koşullu kenar mantığında hata var.

**Çözüm:** Önce koşullu kenar fonksiyonunu (`tools_condition` veya kendi yazdığınız) kontrol edin; gerçekten sonlanma koşuluna ulaşıp ulaşmadığını test edin. Gerçekten uzun sürmesi gereken bir akışsa, `config={"recursion_limit": 50}` ile sınırı yükseltin — ama önce mantık hatası olmadığından emin olun.

## MISSING_CHECKPOINTER

**Ne zaman olur:** [Human-in-the-loop](../orta-seviye/human-in-the-loop.md) (`interrupt()`, `interrupt_before`) ya da [time-travel](../ileri-seviye/time-travel.md) kullanan bir grafı, checkpointer olmadan derlediğinizde.

```
ValueError: Cannot use interrupts without a checkpointer.
```

**Çözüm:** `compile()` çağrısına bir checkpointer verin — geliştirme için `InMemorySaver()`, production için Postgres/Redis (bkz. [Checkpointer & Kalıcılık](../orta-seviye/checkpointer.md)).

## INVALID_CONCURRENT_GRAPH_UPDATE

**Ne zaman olur:** [Paralel çalışan node'lar](../ileri-seviye/send-command.md) (`Send` ile dağıtılan dallar) aynı state alanına, **reducer tanımlanmadan**, aynı anda farklı değerler yazmaya çalıştığında.

```
InvalidUpdateError: At key 'sonuc': Can receive only one value per step.
```

**Çözüm:** O state alanına bir reducer tanımlayın (ör. listeye ekleyen bir reducer) — bkz. [Reducer nedir?](../baslangic/temel-kavramlar.md). Reducer olmadan LangGraph, paralel dallardan gelen çakışan yazmaları nasıl birleştireceğini bilemez.

## INVALID_GRAPH_NODE_RETURN_VALUE

**Ne zaman olur:** Bir node fonksiyonu, state şemasıyla uyumsuz bir değer döndürdüğünde (ör. sözlük yerine string döndürmek).

```
InvalidUpdateError: Expected dict, got 'merhaba'
```

**Çözüm:** Node fonksiyonlarının her zaman **state alanlarını içeren bir sözlük** döndürdüğünden emin olun: `return {"messages": [...]}`, düz bir değer değil.

## MULTIPLE_SUBGRAPHS

**Ne zaman olur:** Aynı [checkpointer](../orta-seviye/checkpointer.md) ile birden fazla [subgraph](../orta-seviye/subgraph-store.md) kullanırken, subgraph'lar arasında checkpoint çakışması oluştuğunda.

**Çözüm:** Her subgraph'ın kendi `compile()` çağrısında ayrı bir checkpointer kullanmadığından ve ana grafın checkpointer'ını doğru şekilde miras aldığından emin olun; genelde subgraph'ı `compile()` **etmeden** (derlemeden) ana grafa node olarak eklemek sorunu çözer.

## INVALID_CHAT_HISTORY

**Ne zaman olur:** Mesaj listesinde bir `AIMessage`'ın `tool_calls`'ına karşılık gelen bir `ToolMessage` eksik olduğunda — model bir aracı çağırmış ama sonucu state'e hiç eklenmemiş.

**Çözüm:** [ToolNode](../orta-seviye/tool-node.md) kullanıyorsanız bu genelde otomatik yönetilir; elle araç sonucu ekliyorsanız her `tool_call_id`'ye karşılık bir `ToolMessage` eklediğinizden emin olun.

---

Daha fazla terim için: [Sözlük](../sozluk.md) · Resmi hata referansı için: [Kaynaklar](../kaynaklar.md)
