# π (pi) — Assistente de Terminal Context-Aware

Um assistente CLI que **conhece seu sistema** antes de responder. Pergunte em linguagem natural e receba o comando exato — já copiado para o clipboard.

```
$ pi "extrair arquivo .tar.xz mantendo permissões"
🤖 pi > Use o tar com as flags apropriadas:

```bash
tar -xJvf arquivo.tar.xz --preserve-permissions
```

──────────────────────────────────────────────────
📋 Comando: tar -xJvf arquivo.tar.xz --preserve-permissions
   ✓ copiado via xclip — Ctrl+Shift+V para colar
──────────────────────────────────────────────────
```

## Por que o pi?

| Antes | Depois |
|-------|--------|
| Abre navegador → ChatGPT → copia → volta pro terminal | `pi "pergunta"` → Ctrl+Shift+V |
| LLM inventa comando que não existe no sistema | Detecta ferramentas instaladas antes de sugerir |
| Precisa de internet | Roda 100% local (modelo próprio) |

## Instalação

```bash
# Instalação rápida
sudo curl -fsSL https://raw.githubusercontent.com/rafaelsadao/pi/main/pi -o /usr/local/bin/pi
sudo chmod +x /usr/local/bin/pi
```

### Requisitos

- **Python 3.8+** (apenas stdlib, zero dependências)
- **llama-server** rodando com API compatível com OpenAI (porta 8080 por padrão)
- Uma das ferramentas de clipboard: `xclip` (X11), `wl-copy` (Wayland), ou nenhuma (fallback OSC 52)

### Configuração

```bash
# URL do servidor LLM (padrão: http://localhost:8080)
export PI_SERVER_URL="http://192.168.1.100:8080"
```

## Uso

```bash
pi "baixar arquivo grande com retomada"
pi "listar processos que mais consomem RAM"
pi "criptografar arquivo com GPG"
pi "encontrar arquivos modificados nas últimas 24h"

# Copiar múltiplos comandos (não só o primeiro)
pi -c "criar usuário e configurar chave SSH"
```

## Como funciona

```
pi "extrair tar.xz"
      │
      ▼
 1. check_tools() → quais ferramentas estão instaladas?
      │
      ▼
 2. Constrói prompt com sistema + ferramentas disponíveis
      │
      ▼
 3. ask_llm() → llama-server (OpenAI-compatible API)
      │
      ▼
 4. Extrai comando do ```bash → destaca em verde
      │
      ▼
 5. Copia pro clipboard (xclip → wl-copy → OSC 52)
```

### Clipboard via OSC 52

Funciona até via **SSH do celular**. O pi usa ANSI OSC 52 como último fallback — seu emulador de terminal recebe o texto e coloca no clipboard do sistema automaticamente.

## Design

- **200 linhas de Python puro** — stdlib only, sem pip install
- **Context-aware** — detecta ferramentas instaladas, elimina alucinação
- **250 tokens de output** — não é chat, é ferramenta
- **Português nativo** — prompt otimizado para pt-BR

## Licença

MIT © 2024 Rafael Sadao
