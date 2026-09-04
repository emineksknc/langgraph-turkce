# langgraph.json & CLI

<span class="badge badge-ileri">🔴 İLERİ / SENIOR</span>

[Production & Deployment](production.md) sayfasında kendi FastAPI sarmalayıcınızı nasıl kuracağınızı gördük. LangGraph Platform'u (managed deployment) kullanmak isterseniz, projenizin kökünde bir `langgraph.json` dosyası ve LangGraph CLI gerekir.

## CLI kurulumu

**CLI** (*Command Line Interface* — komut satırı arayüzü), `langgraph` komutuyla kullanılan, projeyi yerelde çalıştırma/derleme/deploy etme aracıdır.

```bash
pip install langgraph-cli
```

## langgraph.json — proje manifesti

Bu **manifest** (bildirim dosyası), deployment sisteminin projenizi nasıl çalıştıracağını tanımlar: hangi graf(lar)ın nerede olduğu, bağımlılıklar, ortam değişkenleri.

```json title="langgraph.json"
{
  "dependencies": ["."],
  "graphs": {
    "musteri_botu": "./src/graph.py:app"
  },
  "env": ".env",
  "python_version": "3.11"
}
```

- **`graphs`**: her anahtar bir graf adı, değeri `dosya_yolu:degisken_adi` formatında derlenmiş (`compile()` edilmiş) graf nesnesinin nerede olduğunu gösterir.
- **`dependencies`**: `["."]` mevcut dizini pip paketi gibi kurar (yani `pyproject.toml`/`setup.py` gerekir); alternatif olarak `requirements.txt` yolu da verilebilir.
- **`env`**: ortam değişkenlerinin okunacağı `.env` dosyası.

## Yerelde test etme

Deploy etmeden önce, production'daki gibi bir API sunucusunu yerelde ayağa kaldırabilirsiniz:

```bash
langgraph dev
```

Bu komut yerel bir sunucu başlatır ve genelde birlikte gelen bir arayüz (LangGraph Studio) üzerinden grafı görsel olarak inceleyip adım adım çalıştırabilirsiniz — `get_graph().draw_mermaid()` ile statik bir diyagram görmek yerine, gerçek zamanlı, tıklanabilir bir hata ayıklama deneyimi sağlar.

## Build & deploy

```bash
langgraph build -t musteri-botu-image     # Docker image oluşturur
langgraph up                              # yerel Docker Compose ile ayağa kaldırır
```

Managed LangGraph Platform kullanıyorsanız, repo'yu bağladıktan sonra platform bu `langgraph.json` dosyasını okuyarak build/deploy sürecini otomatik yönetir.

## Kendi sarmalayıcınıza karşı ne zaman CLI/Platform?

| Durum | Öneri |
|---|---|
| Hızlı prototip, altyapı yönetmek istemiyorsunuz | `langgraph dev` + LangGraph Platform |
| Mevcut Kubernetes/CI-CD altyapınız var, tam kontrol istiyorsunuz | Kendi FastAPI sarmalayıcınız (bkz. [Production & Deployment](production.md)) |
| Görsel debug, adım adım çalıştırma önemli | `langgraph dev` (LangGraph Studio) |

---

Bu, ileri seviye bölümünün son sayfasıdır. Proje büyüdükçe dosyaları nasıl organize edeceğin için: [Proje Yapısı](proje-yapisi.md).
