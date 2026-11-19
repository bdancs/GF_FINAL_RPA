# AlertaBairro - Tecnologia comunitária para acompanhamento de alagamentos

## Integrantes
- Bianca Carvalho Dancs — RM551645
- Geovanna Silva Cunha — RM97736
- Pedro Emerici Gava — RM551043
- Victor Camargo Maciel — RM98384

Turma: 4ESPX

## Links
- Vídeo[]
- [Aplicativo](https://apps.powerapps.com/play/e/default-11dbbfe2-89b8-4549-be10-cec364e59551/a/272119c6-6fbc-4678-ad9e-1ba678eee01e?tenantId=11dbbfe2-89b8-4549-be10-cec364e59551&hint=bf982d95-4e42-4bc7-93f0-55d0d97c4613&sourcetime=1795054198228)

## Sobre
O AlertaBairro é uma solução criada para permitir que moradores registrem e consultem ocorrências de alagamento em suas regiões.

A proposta busca reunir informações locais em um único ambiente, permitindo que relatos realizados pela própria comunidade ajudem outros moradores a conhecer áreas afetadas e acompanhar a frequência das ocorrências.

## Problema
Em períodos de chuva intensa, moradores podem ter dificuldade para encontrar informações atualizadas sobre ocorrências de alagamento em suas regiões ou em locais para os quais precisam se deslocar. Essas informações podem estar distribuídas entre diferentes fontes.

O AlertaBairro propõe complementar as fontes existentes por meio da participação da própria comunidade, permitindo transformar relatos individuais em informações organizadas e acessíveis a outros moradores.

## Público
A solução é direcionada principalmente a:
- moradores de regiões afetadas por alagamentos;
- trabalhadores que precisam planejar seus deslocamentos;
- comércio local impactado pelas condições das vias.

## Proposta de valor
Centralizar informações sobre ocorrências de alagamento, permitindo que moradores contribuam em suas regiões e consultem registros realizados pela comunidade.

## Sobre o MVP desenvolvido
- Registro de uma ocorrência de alagamento:
  - informar região e endereço;
  - indicar o nível da água;
  - colocar uma descrição e caso queira uma foto
- Consulta registros existentes;
- Validação automática de novos registros;
- Classificação de registros como "Aprovado" ou "Recusado";
- Armazenamento dos dados no SharePoint;
- Análise da frequência de ocorrências por região e ao longo do tempo.

## Arquitetura

Power Apps > Power Automate - AnaliseRegistros > Consulta registros existentes > Verificação de endereço + intervalo de 30 minutos > Aprovado ou Recusado > SharePoint > Power BI

### Processo da automação
1. O usuário registra uma ocorrência pelo Power Apps.
2. O aplicativo envia região, endereço, nível da água e descrição para o fluxo AnaliseRegistros.
3. O Power Automate consulta os registros existentes e verifica:
  A) Se o endereço é o mesmo;
  B) Se foi registrado nos últimos 30 minutos.
4. Caso as condições sejam atendidas, o novo registro recebe status "Recusado".
5. Caso contrário, recebe status "Aprovado".
6. O resultado é gravado na lista de registros.
7. A execução é registrada para auditoria.
8. Em caso de falha, o fluxo executa o tratamento de erro.
9. Os registros aprovados são utilizados para análise no Power BI.

### Power Apps
Responsável pela interface utilizada pelos moradores para registrar e consultar ocorrências.

### Power Automate
Responsável pela automação do processo de validação dos registros.

### SharePoint
Responsável pelo armazenamento estruturado das ocorrências processadas.

### Power BI
Responsável pela visualização e análise da frequência das ocorrências por região e período.

### Objetivo
Como os registros são realizados de forma colaborativa, diferentes moradores podem registrar a mesma ocorrência em um curto intervalo de tempo. A automação AnaliseRegistros funciona como um mecanismo de apoio ao AlertaBairro, identificando possíveis duplicidades e contribuindo para aumentar a qualidade e a confiabilidade dos dados utilizados pela solução.

## Regra de negócio
Um novo registro será considerado duplicado quando existir uma ocorrência:
- no mesmo endereço
- registro anterior com status Aprovado
- registrada nos últimos 30 minutos

Quando todas as condições forem atendidas: Status = Recusado
Quando nenhuma ocorrência equivalente for encontrada: Status = Aprovado

O intervalo de análise é calculado no Power Automate com: addMinutes(utcNow(), -30)

## Estrutura de repetição
1. A automação utiliza a ação Apply to each para percorrer os registros retornados pelo SharePoint List.
2. Para cada item, o fluxo verifica endereço, status e data/hora.
3. Essa estrutura permite processar uma coleção de registros automaticamente, sem intervenção manual.

## Variáveis

### DuplicadoEncontrado :
- Tipo: booleano
- Valor inicial: false
- É alterado para true quando o fluxo identifica uma ocorrência aprovada para o mesmo endereço dentro dos últimos 30 minutos.

### StatusRegistro :
- Tipo: string
- Valor inicial: Aprovado
- Quando uma possível duplicidade é encontrada, o valor é alterado para: Recusado

## Tratamento de exceções
O fluxo possui tratamento específico para falhas durante o processamento.
1. As principais atividades da automação são executadas dentro de um escopo de processamento.
2. Caso esse processamento apresente falha ou ultrapasse o tempo esperado de execução, o escopo de tratamento de erro é acionado.
3. A ocorrência da falha é registrada para auditoria, evitando que falhas ocorram silenciosamente e permitindo sua rastreabilidade.

## Logs e auditoria
A automação mantém registros de execução contendo informações como:
- data e hora
- item processado
- endereço relacionado
- resultado do processamento
- sucesso ou falha
- mensagem associada à execução

## Business Intelligence
O Power BI transforma os registros aprovados em informações agregadas. O painel desenvolvido permite analisar principalmente:
- frequência de ocorrências por região
- comparação da frequência entre diferentes períodos
- evolução mensal dos registros.

O objetivo é identificar quais regiões concentram mais ocorrências e observar possíveis diferenças de frequência ao longo dos meses.

## Segurança e LGPD
O MVP busca minimizar a coleta de informações pessoais. Os principais dados utilizados são relacionados à própria ocorrência:
- região
- endereço da ocorrência
- nível da água
- descrição
- data e hora
- status de validação

A solução evita utilizar informações pessoais desnecessárias dos moradores, e logs e evidências publicados no repositório também não devem expor informações sensíveis.

## Tecnologias utilizadas
- Power Apps
- Power Automate
- SharePoint List
- Power BI
- GitHub

## ODS relacionados
###  ODS 11 - Cidades e Comunidades Sustentáveis
- O AlertaBairro contribui para o acesso a informações comunitárias relacionadas às condições urbanas e ocorrências de alagamento.

### ODS 13 - Ação Contra a Mudança Global do Clima
- A solução contribui para o acompanhamento comunitário de ocorrências relacionadas a eventos de chuva e seus impactos locais.
