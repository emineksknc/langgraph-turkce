# Kurulum

## Gereksinimler

- Python 3.9 veya üstü (3.11+ önerilir)
- Bir LLM sağlayıcı API anahtarı (OpenAI, Anthropic, vb.) — isteğe bağlı, yerel modellerle de çalışabilirsiniz

## Paket kurulumu

=== "pip"

    ```bash
    pip install langgraph langchain langchain-openai
    ```

=== "uv"

    ```bash
    uv add langgraph langchain langchain-openai
    ```

=== "poetry"

    ```bash
    poetry add langgraph langchain langchain-openai
    ```

!!! tip "Sanal ortam kullanın"
    Proje bağımlılıklarını sistemden izole tutmak için `venv` veya `uv venv` ile sanal ortam oluşturmanız önerilir:
    ```bash
    python -m venv .venv
    source .venv/bin/activate  # Windows: .venv\Scripts\activate
    ```

## Ortam değişkenleri

```bash title=".env"
OPENAI_API_KEY=sk-...
# İzleme için (opsiyonel ama önerilir, bkz. Production sayfası)
LANGCHAIN_TRACING_V2=true
LANGCHAIN_API_KEY=lsv2_...
LANGCHAIN_PROJECT=ilk-langgraph-projem
```

## Kurulumu doğrulama

```python
from langgraph.graph import StateGraph
print("LangGraph kuruldu ✔")
```

Hata almadıysanız hazırsınız — sıradaki adım: [Temel Kavramlar](temel-kavramlar.md).
