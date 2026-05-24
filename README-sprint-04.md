## AgroTech

Plataforma Inteligente de Monitoramento Agrícola com API JAVA, .NET, APP REACT, Oracle Autonomous Database, ORDS, APEX e Machine Learning Baseado em Regras

## Antes de continuar, confira nossa aprensentação no YouTube:

[![AgroTech Commercial Presentation](https://img.youtube.com/vi/crMiQIucG7Q/0.jpg)](https://youtu.be/crMiQIucG7Q)

## Visão Geral

O AgroTech é uma plataforma de monitoramento agrícola inteligente desenvolvida utilizando Oracle Cloud Infrastructure (OCI), Oracle Autonomous Database, Oracle REST Data Services (ORDS) e Oracle APEX.

O projeto de machine learning foi criado com o objetivo de transformar leituras técnicas de sensores agrícolas em recomendações compreensíveis para agricultores, permitindo interpretação rápida do ambiente monitorado e facilitando a tomada de decisão operacional.

A solução recebe dados IoT em tempo real, processa as informações utilizando um mecanismo de inteligência contextual e disponibiliza insights através de APIs REST e dashboards inteligentes.

### `Nota dos desenvolvedores:`

_Foi adotado um modelo de inteligência baseado em regras e Machine Learning leve em vez de um LLM devido à necessidade de baixo custo computacional, previsibilidade das respostas e compatibilidade com o Oracle Free Tier. Como o projeto trabalha com dados numéricos estruturados e cenários bem definidos — como umidade, temperatura e irrigação — um mecanismo contextual baseado em regras oferece respostas mais rápidas, determinísticas e fáceis de validar operacionalmente. Além disso, o volume de dados históricos ainda é reduzido para treinamento eficiente de um modelo generativo. A abordagem escolhida também simplifica manutenção, integração com SQL/PLSQL e execução em tempo real diretamente no ecossistema Oracle, sem dependência de APIs externas ou alto consumo de recursos. Para implementação de um LLM seria necessário uma conta com créditos da Oracle e um volume de dados maior para uma acurácia minimamente aceitável. Pensando também no objetivo do projeto, ao longo da última Sprint ficou claro que Machine Learning seria uma estratégia que faria melhor aproveitamento do Autonomous Database, além de que, é mais enxuto e direcionado para o objetivo real do Agrotech: ser barato para o consumidor final - agricultores de pequeno e médio porte._

## Objetivos do Projeto

Objetivos Técnicos

- Centralizar dados agrícolas provenientes de sensores IoT
- Disponibilizar APIs REST modernas utilizando ORDS
- Criar um pipeline de análise inteligente em tempo real
- Transformar leituras técnicas em linguagem natural
- Integrar dados processados ao Oracle APEX
- Construir uma arquitetura distribuída e desacoplada
- Demonstrar uso de tecnologias Oracle Cloud Free Tier

## Objetivos Funcionais

- Entregar valor facilitando a compreensão do agricultor
- Auxiliar agricultores na interpretação de dados ambientais
- Reduzir dependência de dashboards complexos
- Exibir recomendações claras e acionáveis
- Identificar tendências e anomalias ambientais
- Melhorar a percepção operacional do ambiente monitorado

## Arquitetura Geral

```
Sensores IoT
    ↓
Banco RAW (Autonomous AI Database A)
    ↓
ORDS RAW API
    ↓
Banco Inteligente (Autonomous AI Database B - AGROTECH_APP)
    ↓
Tabela SENSOR_CACHE
    ↓
Engine de Insights / ML baseado em regras
    ↓
Tabela SENSOR_INSIGHTS
    ↓
Views analíticas
    ↓
ORDS Inteligente
    ↓
Oracle APEX + React
```

## Estrutura Distribuída

O projeto foi dividido em duas camadas principais:

| **Camada**        | **Responsabilidade**                  |
| :---------------- | :------------------------------------ |
| Banco RAW         | Recebimento das leituras dos sensores |
| Banco Inteligente | Processamento, IA e apresentação      |

`Essa separação foi escolhida para:`

- desacoplar ingestão e inteligência
- permitir escalabilidade futura
- evitar processamento pesado no banco de origem
- facilitar integração via APIs REST
- simular arquitetura enterprise distribuída

## Banco RAW

#### Schema A

User root

### Responsabilidade

Armazenar leituras originais provenientes dos sensores.

### Tabela Principal

ADMIN.SENSOR_READINGS

### Estrutura dos Dados

| **Campo**       | **Tipo**  | **Descrição**           |
| :-------------- | :-------- | :---------------------- |
| ID              | RAW(16)   | Identificador único     |
| CREATED_AT      | TIMESTAMP | Data de criação         |
| HUMIDITY        | NUMBER    | Umidade do ar           |
| LIGHT_INTENSITY | NUMBER    | Intensidade luminosa    |
| RAINFALL        | NUMBER    | Índice de chuva         |
| READING_TIME    | TIMESTAMP | Momento da leitura      |
| SOIL_MOISTURE   | NUMBER    | Umidade do solo         |
| SOIL_PH         | NUMBER    | pH do solo              |
| TEMPERATURE     | NUMBER    | Temperatura ambiente    |
| UPDATED_AT      | TIMESTAMP | Última atualização      |
| WIND_DIRECTION  | NUMBER    | Direção do vento        |
| WIND_SPEED      | NUMBER    | Velocidade do vento     |
| SENSOR_ID       | RAW(16)   | Identificador do sensor |

## Exposição REST com ORDS

### Objetivo

Transformar os dados do banco RAW em APIs REST consumíveis por:

- Oracle APEX
- aplicações React
- engine de insights
- serviços externos

### Habilitação do Schema no ORDS

```sql
BEGIN
  ORDS.ENABLE_SCHEMA(
    p_enabled => TRUE,
    p_schema => 'RUAN'
  );
END;
/
```

## Endpoint REST RAW

### Endpoint

```
/ords/ruan/agrotech/sensor-readings/latest/:minutes
```

### Objetivo

Retornar leituras recentes dos sensores.

## Handler ORDS

```sql
BEGIN


  ORDS.DEFINE_HANDLER(
    p_module_name => 'agrotech',
    p_pattern     => 'sensor-readings/latest/:minutes',
    p_method      => 'GET',
    p_source_type => ORDS.source_type_query,
    p_source      => q'[
        SELECT
            RAWTOHEX(ID) AS ID,
            CREATED_AT,
            HUMIDITY,
            LIGHT_INTENSITY,
            RAINFALL,
            READING_TIME,
            SOIL_MOISTURE,
            SOIL_PH,
            TEMPERATURE,
            UPDATED_AT,
            WIND_DIRECTION,
            WIND_SPEED,
            RAWTOHEX(SENSOR_ID) AS SENSOR_ID
        FROM ADMIN.SENSOR_READINGS
        WHERE READING_TIME >=
              CURRENT_TIMESTAMP
              - NUMTODSINTERVAL(:minutes, 'MINUTE')
        ORDER BY READING_TIME DESC
    ]'
  );


  COMMIT;


END;
/
```

### Exemplo de JSON Retornado

```json
{
  "items": [
    {
      "humidity": 85.58,
      "soil_moisture": 59.91,
      "soil_ph": 6.86,
      "temperature": 20.73,
      "wind_speed": 8.93
    }
  ]
}
```

## Banco Inteligente

### Schema B

AGROTECH_APP

### Responsabilidades

- consumir APIs RAW
- armazenar cache inteligente
- gerar insights
- expor APIs inteligentes
- alimentar o Oracle APEX

## Estratégia de Cache

### Tabela

SENSOR_CACHE

### Objetivo

Evitar dependência direta do endpoint RAW em tempo real.

### Benefícios

- redução de chamadas externas
- maior estabilidade
- processamento desacoplado
- melhor desempenho
- persistência histórica

## Sincronização Automática

### Procedure

SYNC_SENSOR_CACHE

```sql
Scheduler
BEGIN


  DBMS_SCHEDULER.CREATE_JOB (
    job_name        => 'SYNC_SENSOR_CACHE_JOB',
    job_type        => 'PLSQL_BLOCK',
    job_action      => 'BEGIN SYNC_SENSOR_CACHE; END;',
    repeat_interval => 'FREQ=MINUTELY;INTERVAL=1',
    enabled         => TRUE
  );


END;
/
```

## Modelo de Machine Learning Utilizado

### Estratégia adotada

Foi utilizado inicialmente um modelo de inteligência baseado em regras contextuais.

## Justificativa Técnica

A escolha foi feita considerando:

- curto tempo de desenvolvimento
- pequeno volume de dados históricos
- necessidade de previsibilidade das recomendações
- facilidade de manutenção
- compatibilidade com Oracle Free Tier
- baixo custo computacional

## Funcionamento do Modelo

### O mecanismo analisa:

- umidade do solo
- umidade do ar
- temperatura
- intensidade luminosa
- velocidade do vento
- pH do solo

### Com base nos intervalos definidos, o sistema classifica os dados em:

```
Nível           Significado
LOW             Estável
MEDIUM          Atenção
HIGH            Crítico
```

## Estrutura das Recomendações

Cada insight possui:

```
Campo	                 Objetivo
MESSAGE	                 Resumo principal
REASON	                 Motivo da recomendação
ACTION_RECOMMENDED	     Ação sugerida
EXPECTED_OUTCOME	     Resultado esperado
```

## Exemplo de Insight

Categoria: STABILITY

Mensagem:
Condições ambientais estáveis.

Motivo:
Os indicadores ambientais estão dentro da faixa esperada.

Ação:
Mantenha o monitoramento regular da plantação.

Objetivo:
Manter estabilidade ambiental para o cultivo.

## Tabela de Insights

SENSOR_INSIGHTS

### Objetivo

Persistir recomendações geradas pelo engine inteligente.

## View Analítica Principal

VW_HOME_INSIGHTS

### Objetivo

Retornar:

- insight mais recente
- insight histórico
- última anomalia relevante

## Estratégia de UX Inteligente

A Home não exibe apenas múltiplos eventos iguais.

Ela apresenta:

```
Tipo	        Objetivo
Atual	        Estado presente
Histórico	    Comparação temporal
Anomalia	    Evento relevante recente
```

Essa abordagem foi escolhida para:

- transmitir evolução temporal
- evitar repetição visual
- aumentar percepção de inteligência
- melhorar experiência do agricultor
- Endpoint Inteligente

### Endpoint

```
/ords/agrotech_app/insights/latest
```

## Handler ORDS Inteligente

```sql
BEGIN


  ORDS.DEFINE_HANDLER(
    p_module_name => 'agrotech.insights',
    p_pattern     => 'latest',
    p_method      => 'GET',
    p_source_type => ORDS.source_type_query,


    p_source => q'[
        SELECT
            SENSOR_ID,
            READING_TIME,
            RISK_LEVEL,
            CATEGORY,
            MESSAGE,
            REASON,
            ACTION_RECOMMENDED,
            EXPECTED_OUTCOME,
            SCORE
        FROM VW_HOME_INSIGHTS
        ORDER BY READING_TIME DESC
    ]'


  );


  COMMIT;


END;
/
```

## Integração com Oracle APEX

### Estratégia Visual

A Home do APEX foi transformada em um painel inteligente em tempo real.

### Recursos Implementados

#### Cards Inteligentes

Cada card exibe:

- nível de risco
- recomendação
- ação sugerida
- horário da leitura

### Atualização em Tempo Real

```
setInterval(loadAgroInsights, 30000);
```

### Objetivo

Atualizar insights automaticamente a cada 30 segundos.

## Justificativa da Escolha do APEX

Oracle APEX foi escolhido devido:

- integração nativa com Oracle Database
- rapidez no desenvolvimento
- suporte REST integrado
- baixo custo operacional
- excelente produtividade
- compatibilidade com OCI Free Tier

###Justificativa da Escolha do ORDS

ORDS foi escolhido por:

- exposição rápida de APIs REST
- integração nativa com Oracle
- simplicidade operacional
- arquitetura moderna baseada em REST
- facilidade de integração com frontend
- excelente suporte para microsserviços

## Justificativa da Arquitetura Distribuída

A separação entre banco RAW e banco inteligente foi adotada para:

- simular arquitetura enterprise
- desacoplar ingestão e análise
- evitar impacto no banco principal
- permitir escalabilidade futura
- melhorar organização da solução

### Possíveis Evoluções Futuras

- Oracle Machine Learning
- regressão temporal
- previsão de irrigação
- previsão climática
- detecção de anomalias

## IA Generativa

Possível integração futura com:

Oracle Generative AI

- geração automática de relatórios
- linguagem natural avançada
- resumos agrícolas inteligentes

## Aplicações Futuras

- agronegócio
- estufas inteligentes
- agricultura de precisão
- monitoramento climático
- fazendas conectadas
- automação agrícola

## Tecnologias Utilizadas

| **Tecnologia**              | **Finalidade**         |
| :-------------------------- | :--------------------- |
| Oracle Cloud Infrastructure | Infraestrutura         |
| Oracle Autonomous Database  | Banco de dados         |
| Oracle ORDS                 | APIs REST              |
| Oracle APEX                 | Frontend               |
| JavaScript                  | Atualização dinâmica   |
| CSS Customizado             | Interface moderna      |
| SQL/PLSQL                   | Regras e processamento |

## Conclusão

O AgroTech demonstra como tecnologias Oracle podem ser utilizadas para construir uma plataforma agrícola inteligente, moderna e escalável.

A solução vai além de dashboards tradicionais, transformando dados técnicos em recomendações contextualizadas e compreensíveis.

O projeto combina:

- **IoT**
- **APIs REST**
- **inteligência contextual**
- **Oracle Cloud**
