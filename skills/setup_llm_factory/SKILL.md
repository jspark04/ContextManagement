---
name: setup_llm_factory
description: Sets up a flexible LLM factory pattern to support multiple providers (OpenAI, Gemini) and model tiers (Fast, Smart).
---

# Setup LLM Factory

Use this skill when you need to configure an application to support multiple LLM providers (e.g., OpenAI, Google Gemini, Anthropic) and/or enforce cost-efficiency through model tiering ("Fast" vs "Smart").

## 1. Configure Settings

Add the following to your `config.py` (using `pydantic-settings`):

```python
class Settings(BaseSettings):
    # LLM Provider
    llm_provider: str = "openai"  # "openai" or "gemini"

    # OpenAI Models
    openai_api_key: str = ""
    openai_fast_model: str = "gpt-4o-mini"
    openai_smart_model: str = "gpt-4"

    # Gemini Models
    gemini_api_key: str = ""
    gemini_fast_model: str = "gemini-2.0-flash-001"
    gemini_smart_model: str = "gemini-2.0-flash-001" 
```

## 2. Create `llm.py` Factory

Create a module `app/llm.py` with a factory function:

```python
from langchain_core.language_models import BaseChatModel
from langchain_openai import ChatOpenAI
from langchain_google_genai import ChatGoogleGenerativeAI
from app.config import get_settings

def get_llm(json_mode: bool = False, temperature: float = 0.7, model_tier: str = "fast") -> BaseChatModel:
    settings = get_settings()
    provider = settings.llm_provider.lower()

    if provider == "openai":
        if not settings.openai_api_key:
             raise ValueError("OpenAI API key missing.")
        
        model = settings.openai_fast_model if model_tier == "fast" else settings.openai_smart_model
        kwargs = {"response_format": {"type": "json_object"}} if json_mode else {}
        
        return ChatOpenAI(
            model=model, 
            api_key=settings.openai_api_key, 
            temperature=temperature, 
            model_kwargs=kwargs
        )

    elif provider == "gemini":
        if not settings.gemini_api_key:
            raise ValueError("Gemini API key missing.")
            
        model = settings.gemini_fast_model if model_tier == "fast" else settings.gemini_smart_model
        
        return ChatGoogleGenerativeAI(
            model=model,
            google_api_key=settings.gemini_api_key,
            temperature=temperature,
            convert_system_message_to_human=True
        )
    
    raise ValueError(f"Unknown provider: {provider}")
```

## 3. Usage Pattern

In your agents or logic:

```python
from app.llm import get_llm

# For simple tasks (Fast tier)
llm_fast = get_llm(model_tier="fast")

# For complex reasoning (Smart tier)
llm_smart = get_llm(model_tier="smart", temperature=0.2)
```

## 4. Verification

Always create a script (e.g., `scripts/verify_llm.py`) to test connection to both providers/tiers.
