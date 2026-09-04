# Koşullu Kenarlar

<span class="badge badge-baslangic">🟢 BAŞLANGIÇ</span>

Bir node'dan sonra hangi node'a gidileceği, sabit değil, **state'e bakılarak** karar veriliyorsa **koşullu kenar** (*conditional edge* — bazı kaynaklarda "dallanma"/*branching* olarak da geçer) kullanılır.

## Temel örnek

```python
def yon_bul(state: State) -> str:
    son_mesaj = state["messages"][-1]
    if son_mesaj.tool_calls:
        return "arac"
    return END

graph.add_conditional_edges(
    "chatbot",   # hangi node'dan sonra
    yon_bul,     # karar fonksiyonu
)
```

`yon_bul` fonksiyonu bir string döndürür; bu string, gidilecek node'un adıyla eşleşmelidir (ya da `END`).

## path_map ile açık eşleme (önerilir)

```python
graph.add_conditional_edges(
    "chatbot",
    yon_bul,
    {"arac": "tools", "son": END},  # path_map: dönüş değeri -> node adı
)
```

!!! warning "Sık yapılan hata"
    `path_map` vermezseniz, `yon_bul`'un döndürdüğü string'in **birebir** bir node adıyla eşleşmesi gerekir. Yazım hatası (`"arac"` yerine `"araç"`) grafın derlenmesinde ya da çalışma zamanında sessiz/anlaşılması zor hatalara yol açabilir. `path_map` ile bu eşlemeyi açıkça yazmak hata ayıklamayı kolaylaştırır.

## Döngü kurmak

Koşullu kenarı, bir node'a geri dönecek şekilde de kurabilirsiniz — bu, "araç çağır → sonucu değerlendir → gerekirse tekrar çağır" döngüsünün temelidir:

```python
graph.add_edge("tools", "chatbot")  # araçtan sonra tekrar chatbot'a dön
```

Bu döngü potansiyel olarak sonsuz sürebileceği için `recursion_limit` (döngü üst sınırı) ile üst sınır koymanız önerilir (bkz. [Production sayfası](../ileri-seviye/production.md)).

---

Başlangıç bölümü burada tamamlanıyor. Sıradaki adım: [ToolNode & Hazır Ajanlar](../orta-seviye/tool-node.md).

📄 Bu seviyenin özetini PDF olarak indir: [Başlangıç Cheatsheet](../assets/cheatsheets/langgraph-baslangic-cheatsheet.pdf)
