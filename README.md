# Projeto SOSZap: Simulação de Atendente de Emergência

Este projeto implementa uma simulação básica de um atendente virtual para lidar com chamadas de emergência recebidas via mensagem. Ele classifica a emergência com base em palavras-chave e interage com o usuário para coletar dados de localização, utilizando também um agente baseado em Google Gemini ADK para gerar uma resposta final.

## Funcionalidades

* **Classificação de Emergência:** Analisa a mensagem recebida e classifica a emergência como `POLÍCIA` (casos de violência, assalto, etc.) ou `SAMU` (casos médicos) usando uma lógica baseada em palavras-chave.
* **Coleta de Localização:** Guia o usuário para fornecer sua localização, seja através do compartilhamento automático do WhatsApp (simulado) ou digitação manual, incluindo ponto de referência.
* **Geração de Resposta:** Utiliza um agente baseado em Google Gemini (via Google ADK) para gerar uma mensagem de resposta empática e informativa para o solicitante após a classificação inicial e coleta de dados.
* **Processamento de Texto:** Inclui uma função utilitária para remover acentos das mensagens, facilitando a correspondência de palavras-chave.

## Pré-requisitos

* Python 3.7+
* Conta Google Cloud com acesso aos modelos Gemini.
* Uma API Key do Google Cloud configurada.
* Preferencialmente executado em um ambiente como Google Colab para facilitar a configuração e o uso do `google-adk`.

## Instalação

1.  **Clone o repositório** (ou salve o código como um arquivo Python, por exemplo, `sos_agent.py`).

2.  **Instale as dependências** usando pip. Se estiver no Google Colab, use os comandos `%pip` ou `!pip` presentes no código:

    ```bash
    pip install google-genai google-adk
    ```
    ou no Colab:
    ```python
    %pip -q install google-genai
    !pip install -q google-adk
    ```

## Configuração

1.  **Obtenha sua Google API Key:** Siga as instruções da documentação do Google Cloud para obter uma chave de API com acesso aos modelos Gemini.

2.  **Configure a API Key:**
    * **No Google Colab:** O código fornecido assume que a API Key está armazenada nos segredos do Colab sob o nome `GOOGLE_API_KEY`. Vá em "Secrets" (ícone de chave) no menu lateral do Colab e adicione uma nova entrada com o nome `GOOGLE_API_KEY` e o valor da sua chave.
    * **Fora do Colab:** Defina a variável de ambiente `GOOGLE_API_KEY` antes de executar o script:
        ```bash
        export GOOGLE_API_KEY='SUA_API_KEY_AQUI'
        ```
        ou diretamente no script (não recomendado para produção):
        ```python
        import os
        os.environ["GOOGLE_API_KEY"] = "SUA_API_KEY_AQUI"
        ```

## Como Executar

Para rodar este projeto, siga os passos abaixo:

1.  **Salve o Código:** Copie todo o código Python fornecido e salve-o em um arquivo com a extensão `.py` (por exemplo, `sos_agent.py`).

2.  **Certifique-se de ter os Pré-requisitos e a Configuração:** Verifique se você tem Python instalado, as bibliotecas necessárias (`google-genai`, `google-adk`) instaladas e sua `GOOGLE_API_KEY` configurada conforme descrito nas seções "Pré-requisitos" e "Configuração".

3.  **Escolha o Ambiente de Execução:**

    ### Executando no Google Colab

    Este é o ambiente recomendado, pois a configuração da ADK e da chave API é mais direta usando os recursos do Colab.

    * Abra um novo notebook Google Colab.
    * Cole **todo** o código Python em uma célula de código.
    * Certifique-se de que sua `GOOGLE_API_KEY` está configurada nos segredos do Colab (conforme explicado na seção "Configuração").
    * Execute a célula de código (clique no botão de play ou pressione `Shift + Enter`).
    * A saída do script, incluindo as etapas da simulação e as perguntas interativas, aparecerá diretamente abaixo da célula.

    ### Executando via Linha de Comando

    Você pode executar o script diretamente do seu terminal, desde que tenha Python e as bibliotecas instaladas e a variável de ambiente `GOOGLE_API_KEY` configurada.

    * Abra um terminal ou prompt de comando.
    * Navegue até o diretório onde você salvou o arquivo `sos_agent.py`.
    * Execute o script usando o interpretador Python:
        ```bash
        python sos_agent.py
        ```
    * A simulação começará, e você precisará interagir com o terminal, respondendo às perguntas sobre localização quando solicitado.

4.  **Interação durante a Execução:** O script está configurado com exemplos no bloco `if __name__ == "__main__":`. Ao executar, ele simulará o recebimento de mensagens de emergência e fará perguntas interativas sobre a localização. Responda `sim` ou `não` (ou variações aceitas) e forneça as informações de localização quando pedido.

## Estrutura do Código

* `remover_acentos(txt)`: Função auxiliar para normalizar strings, removendo acentos e convertendo para minúsculas.
* `pergunta_sim_nao(pergunta)`: Função auxiliar para interagir com o usuário com perguntas sim/não, validando a resposta.
* `eh_resposta_sim(resposta)`: Função auxiliar simples para verificar se a resposta é uma variação de "sim".
* `classificar_emergencia(mensagem)`: Função principal para classificar a emergência com base em listas de palavras-chave (stems), retornando o tipo (`POLÍCIA`, `SAMU`, `INDEFINIDO`) e uma resposta pré-definida.
* `capturar_localizacao()`: Função para interagir com o usuário, solicitando e confirmando dados de localização (simulando a opção de compartilhar no WhatsApp ou digitar manualmente, incluindo referência).
* `simular_fluxo(mensagem)`: Orquestra o fluxo completo da simulação para uma dada mensagem de emergência, chamando a classificação, coleta de localização e, finalmente, o agente ADK.
* Configuração da API Gemini e importação do ADK.
* `call_agent(agent, message_text)`: Função auxiliar para executar um agente ADK com uma mensagem e capturar a resposta final.
* `to_markdown(text)`: Função auxiliar para formatar saída em Markdown (útil no Colab).
* `agente_solicitacao(...)`: Definição e configuração do agente Gemini via ADK (`gemini-2.0-flash`), com instrução para gerar uma mensagem calmante confirmando o contato com as autoridades.
* Bloco `if __name__ == "__main__":`: Contém exemplos de uso da função `simular_fluxo` para demonstrar o fluxo do projeto.

## Observações

* A classificação inicial em `classificar_emergencia` é baseada em uma lista simples de palavras-chave e é feita *antes* de chamar o agente ADK.
* O agente ADK (`agente_solicitacao`) é usado *após* a classificação por palavras-chave e coleta de localização para gerar a *resposta final* ao usuário, seguindo sua instrução interna para ser empático e confirmar o acionamento.
* Este projeto é uma **simulação**. Ele não se conecta a uma API real do WhatsApp nem aciona serviços de emergência de fato.
* A parte do agente ADK (`agente_solicitacao`) usa o modelo `gemini-2.0-flash` e inclui a ferramenta `Google Search`, embora a instrução atual do agente não a utilize ativamente para responder ao usuário.

## Possíveis Melhorias Futuras

* Integrar com uma API real do WhatsApp para receber e enviar mensagens de fato.
* Utilizar o próprio modelo Gemini (ou o agente ADK) para realizar a **classificação da emergência** de forma mais robusta e flexível, em vez de depender apenas de palavras-chave fixas.
* Expandir os tipos de emergência reconhecidos.
* Implementar um loop contínuo para processar múltiplas mensagens recebidas sequencialmente.
* Adicionar tratamento de erros mais robusto para entradas inválidas ou problemas na API/rede.
* Melhorar a lógica de coleta e validação da localização, talvez integrando com serviços de geolocalização.
* Refinar a instrução do agente para gerar respostas ainda mais personalizadas, considerando o tipo específico da emergência classificada.
