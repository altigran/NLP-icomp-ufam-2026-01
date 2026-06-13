# Construindo um Agente de IA do Zero

Sequência de notebooks que constrói, de forma incremental, um agente de IA a partir de um único
laço de controle — sem frameworks. Baseada no artigo *"Building an AI Agent from Scratch: No Magic,
Just a Deterministic Loop"* (Sergey Nes) e adaptada para usar **exclusivamente o endpoint gratuito
da NVIDIA**.

## Pré-requisitos

- **Python ≥ 3.10** (Google Colab e Jupyter funcionam sem ajustes).
- Uma **API key da NVIDIA**: criar conta em <https://build.nvidia.com> e gerar uma chave (prefixo
  `nvapi-`). O uso é gratuito.
- Dependências instaladas pela primeira célula de cada notebook (`pip install openai`; o notebook 06
  também instala `fastmcp`). Nada além disso.

## Como fornecer a chave

A célula de **Configuração** de cada notebook procura a chave em três lugares, nesta ordem:

1. **Colab Secrets** (recomendado no Colab) — no painel esquerdo, clique no ícone de chave (🔑),
   adicione um secret de nome `NVIDIA_API_KEY` com o valor `nvapi-...` e ative *Acesso do notebook*.
   A chave fica guardada na sua conta e é detectada automaticamente, sem precisar colar de novo.
2. **Variável de ambiente** `NVIDIA_API_KEY` (útil em Jupyter local):
   ```bash
   export NVIDIA_API_KEY="nvapi-..."
   ```
3. **Entrada interativa** — se nenhuma das anteriores existir, a chave é solicitada na hora
   (`getpass`) e não fica salva no arquivo.

## Modelo

O modelo padrão é **`meta/llama-3.1-8b-instruct`** (~1 s por chamada, suporte confiável a
*function calling*). Para trocar de modelo, altere a variável `MODEL` na célula de configuração por
outro identificador do catálogo (<https://build.nvidia.com/models>); nenhum outro ponto do código
muda. Toda a infraestrutura é da NVIDIA — não há dependência de serviços pagos ou modelos locais.

## Sequência dos notebooks

Cada notebook é autossuficiente (configuração + ferramentas + laço) e deve ser estudado na ordem.
Clique no badge para abrir diretamente no Google Colab:

| # | Arquivo | Conceito introduzido | Colab |
|---|---------|----------------------|-------|
| 01 | `01_loop_minimo.ipynb` | O agente como laço de controle; uma ferramenta; a condição de parada e `tool_choice`. | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altigran/NLP-icomp-ufam-2026-01/blob/main/01_loop_minimo.ipynb) |
| 02 | `02_varias_tools.ipynb` | Composição de múltiplas ferramentas; o histórico (`messages`) como memória de trabalho. | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altigran/NLP-icomp-ufam-2026-01/blob/main/02_varias_tools.ipynb) |
| 03 | `03_trocando_modelo.ipynb` | O modelo como componente substituível e a dependência em relação ao *function calling*. | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altigran/NLP-icomp-ufam-2026-01/blob/main/03_trocando_modelo.ipynb) |
| 04 | `04_robustez.ipynb` | Isolamento de falhas, *retry*, terminação e conformidade com a plataforma. | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altigran/NLP-icomp-ufam-2026-01/blob/main/04_robustez.ipynb) |
| 05 | `05_delegacao.ipynb` | Delegação hierárquica: um modelo pequeno orquestra e delega o raciocínio a um maior. | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altigran/NLP-icomp-ufam-2026-01/blob/main/05_delegacao.ipynb) |
| 06 | `06_mcp.ipynb` | Desacoplamento de ferramentas via Model Context Protocol (MCP). | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/altigran/NLP-icomp-ufam-2026-01/blob/main/06_mcp.ipynb) |

> Os badges apontam para a raiz do branch `main` do repositório
> `altigran/NLP-icomp-ufam-2026-01`. Ajuste o caminho/branch nos links se a estrutura for outra.
>
> O notebook **06** usa `async`/`await` e o pacote `fastmcp`; execute-o em Jupyter ou Colab (não em
> um script Python comum).

## Gabaritos

A pasta `gabarito/` contém cópias dos seis notebooks **já executadas**, com as saídas reais
embutidas, para conferência. Os notebooks da raiz não trazem saídas, para que sejam executados do
zero.

## Nota sobre o endpoint da NVIDIA

Os modelos llama hospedados aceitam **apenas uma invocação de ferramenta por turno**; o laço dos
notebooks trata disso automaticamente. Modelos sem suporte a *function calling* (p. ex.,
`google/gemma-2-2b-it`) rejeitam a requisição com erro HTTP — comportamento explorado no notebook 03.
