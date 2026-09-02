# LangGraph Türkçe Rehber

Sıfırdan senior seviyeye LangGraph — Türkçe kapsamlı kaynak. [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) ile hazırlanmıştır.

## Yerelde çalıştırma

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

Tarayıcıda `http://127.0.0.1:8000` adresini aç. Dosya kaydettiğinde sayfa otomatik yenilenir.

## Yeni sayfa ekleme

1. İlgili klasöre (`docs/baslangic/`, `docs/orta-seviye/`, `docs/ileri-seviye/`, `docs/referans/`) bir `.md` dosyası ekle.
2. `mkdocs.yml` içindeki `nav:` bölümüne dosyayı ekle.
3. `mkdocs serve` çalışırken tarayıcıda otomatik görünür.

## GitHub Pages'e deploy

`main` branch'ine push ettiğinde `.github/workflows/deploy.yml` otomatik olarak siteyi derleyip `gh-pages` branch'ine yayınlar. Reponun **Settings → Pages** kısmından kaynak olarak `gh-pages` branch'ini seçmen yeterli (bir kere).

Elle deploy etmek istersen:

```bash
mkdocs gh-deploy --force
```

## Lisans / Kaynak

Bu, LangGraph (MIT lisanslı, langchain-ai) hakkında bağımsız bir öğretim kaynağıdır. LangChain Inc. ile bağlantılı değildir.
