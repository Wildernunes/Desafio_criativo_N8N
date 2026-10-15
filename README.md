🚀 Automação de Captura de Leads com N8N, Google Sheets e Gmail
Este repositório contém a documentação e a estrutura lógica para a criação de um fluxo de automação no N8N para captura, validação, registro e notificação de novos leads vindos de formulários.
---
📌 Prompt de Entrada
```text
Quero criar uma automação no N8N para registrar automaticamente leads recebidos por formulário.

Público ou responsável:
Equipe comercial.

Resultado esperado:
Salvar os dados em uma planilha e enviar uma notificação para o time de vendas.

Ferramentas envolvidas:
Google Forms, Google Sheets e Gmail.

Fluxo desejado:
1. Receber uma nova resposta do formulário.
2. Registrar os dados na planilha.
3. Enviar um e-mail de confirmação.

Regras importantes:
Ignorar registros sem e-mail válido.
```
---
🛠️ Solução Proposta & Guia Técnico
🎯 Visão Geral do Workflow
O fluxo captura novas respostas enviadas via Google Forms, aplica uma validação para garantir a integridade do campo de e-mail, insere o registro verificado em uma planilha do Google Sheets e dispara um e-mail com os detalhes do lead via Gmail para a equipe comercial.
---
🧱 Nós (Nodes) do N8N Utilizados
Ordem	Nome do Nó	Tipo	Função
1	Google Forms Trigger / Google Sheets Trigger	Trigger	Capturar novos envios de formulário em tempo real.
2	If / Filter	Lógico	Validar o preenchimento e o formato do e-mail.
3	Google Sheets	Ação	Registrar as informações do lead na planilha de vendas.
4	Gmail	Ação	Enviar um e-mail formatado com a notificação do novo lead.
---
⚙️ Lógica de Funcionamento e Configuração
```text
[ Trigger: Formulário ] ──► [ Filter: Valida E-mail ] ──► (Sim) ──► [ Google Sheets ] ──► [ Gmail ]
                                                     └──► (Não) ──► [ Encerrar Fluxo ]
```
1. Captura do Form (`Trigger`)
Nó: `Google Sheets Trigger` (Evento: Row Added) ou `Google Forms Trigger`.
Descrição: Monitora novos envios na planilha conectada ao formulário.
Campos Extraídos: `Timestamp`, `Nome`, `E-mail`, `Telefone`, `Empresa`.
2. Validação de Dados (`Filter / If`)
Regra: Filtrar e ignorar registros sem e-mail ou com formato incorreto.
Validação (Regex): `^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`
Fluxo: Se o e-mail for válido, prossegue; caso contrário, a execução é encerrada.
3. Registro do Lead (`Google Sheets`)
Operação: `Append Row`
Mapeamento:
`Data/Hora` ➔ `{{ $json["Timestamp"] }}`
`Nome` ➔ `{{ $json["Nome"] }}`
`E-mail` ➔ `{{ $json["E-mail"] }}`
`Telefone` ➔ `{{ $json["Telefone"] }}`
4. Notificação Comercial (`Gmail`)
Operação: `Send Email`
Destinatário: `vendas@suaempresa.com`
Assunto: `🚀 Novo Lead Recebido: {{ $json["Nome"] }}`
Corpo (HTML):
```html
  <h3>Novo Lead Cadastrado!</h3>
  <p><strong>Nome:</strong> {{ $json["Nome"] }}</p>
  <p><strong>E-mail:</strong> {{ $json["E-mail"] }}</p>
  <p><strong>Telefone:</strong> {{ $json["Telefone"] }}</p>
  <p>Os dados foram devidamente salvos na planilha comercial.</p>
  ```
---
💡 Boas Práticas Recomendadas
Credenciais OAuth2: Garanta que as permissões de escopo estejam habilitadas para leitura/escrita no Google Sheets e envio no Gmail.
Tratamento de Erros: Habilite o nó de `Error Trigger` para alertar administradores em caso de falhas de conexão nas APIs.
