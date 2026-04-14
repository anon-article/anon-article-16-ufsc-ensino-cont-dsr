# 📊 Pipeline ETL: Validador, Sanitizador e Injetor - Anonymous Author

Esta pasta contém o utilitário de processamento de dados (`main.py`) desenvolvido para o projeto **Anonymous Author**.

Sua função é atuar como uma **camada de auditoria, limpeza e validação (ETL)**, transformando os dados brutos gerados (arquivos `.json`) em dados confiáveis para a aplicação. O script cruza informações com o gabarito oficial, injeta licenciamento e, crucialmente, **remove alucinações de classificação** através de uma lista de tags permitidas.

---

## 🚀 Funcionalidades

* **Sanitização Taxonômica:** Filtra as tags de cada questão comparando-as com a `ementa.json` oficial. Classificações alucinadas pela IA são removidas e o script registra no log exatamente qual questão e qual tag foi interceptada, criando uma trilha de auditoria técnica.
* **Conciliação de Gabarito:** Cruza o ID da questão com o arquivo de texto (`.txt`) oficial da banca, garantindo 100% de fidelidade à resposta correta.
* **Injeção de Metadados (TASL):** Adiciona automaticamente o cabeçalho de licenciamento (CC-BY-SA 4.0) e atribuição correta em todos os arquivos processados.
* **Padronização de Dados:**
    * Converte IDs para inteiros.
    * Identifica e marca questões anuladas (`X` ou `*`).
    * Preenche lacunas de texto para evitar erros no frontend.
* **Relatórios Matriciais:** Gera planilhas (`.csv`) comparando a contagem de questões extraídas *versus* a contagem oficial do Edital.

---

## 🛠️ Pré-requisitos

O script foi projetado para ser **leve e nativo**.

* **Python 3.8+** (Nenhuma biblioteca externa necessária).

Para rodar:
```bash
python main.py

```

---

## 📂 Estrutura de Arquivos

O script espera a seguinte organização para funcionar plenamente:

```text
/ (Raiz do Projeto)
│
├── main.py                # O motor de processamento
├── ementa.json            # [IMPORTANTE] A lista oficial de tags permitidas
├── README.md              # Documentação
│
├── importar/              # ENTRADA DE DADOS
│   ├── oficial.csv        # Dados quantitativos do Edital (opcional)
│   ├── CFC_2024_01.json   # Arquivo bruto (gerado pela IA)
│   └── CFC_2024_01.txt    # Gabarito oficial (digitado manualmente)
│
└── exportar/              # SAÍDA DE DADOS (Sanitizada)
    ├── CFC_2024_01.json   # Arquivo limpo e pronto para o site
    └── relatorio_geral.csv # Auditoria de extração

```

---

## ⏯️ Como Usar (Fluxo de Trabalho)

### 1. Preparação

Certifique-se de que o arquivo `ementa.json` esteja na mesma pasta do script. Ele serve como o "porteiro" das tags válidas.

### 2. Importação

Coloque na pasta `importar/` os pares de arquivos da prova. Eles devem ter o **mesmo nome base**:

* **O JSON:** O arquivo contendo as questões extraídas.
* **O TXT:** Gabarito simples (formato `1-A`, `2-C`, `3-X`).

### 3. Execução

Rode o script: `python main.py`.

O terminal exibirá o log de auditoria detalhado:

```text
> INICIANDO ARQUIVO: CFC_2022_01.json
   [LENDO] Buscando gabarito em: CFC_2022_01.txt
   [CORREÇÃO] Q24 (Contabilidade Setor Público): Removida(s) ['NBC TSP 08']
   [LIMPEZA] Total de 1 tags inválidas removidas neste arquivo.
```

### 4. Auditoria

Ao final, responda `s` para gerar o relatório comparativo. O arquivo CSV gerado na pasta `exportar` mostrará se alguma disciplina está com contagem divergente do `oficial.csv`.

---

## 📝 Detalhes da Validação

O script realiza as seguintes transformações (Transform) nos dados:

| Campo | Ação do Script |
| --- | --- |
| `tags` | **Filtro Rígido:** Remove qualquer string que não exista na `ementa.json`. |
| `id` | Força conversão para **Inteiro** (remove zeros à esquerda). |
| `gabarito` | Substitui o valor da IA pelo valor do **TXT oficial**. |
| `gabarito_texto` | Busca o texto correspondente à letra correta nas opções. |
| `anulada` | Marca `true` se o gabarito for `X`, `*` ou `NULA`. |
| `resolucao` | Garante que o campo exista (string vazia) se estiver ausente. |

---

## ⚖️ Licença

Este utilitário é distribuído sob a licença **AGPL-3.0**.
Os dados processados (conteúdo das questões) são atribuídos sob a licença **CC-BY-SA 4.0**. Você pode alterar as licenças dos arquivos gerados por você.