# Projeto AgroTech - Arquitetura e Solução 

## Objetivo do Projeto

O AgroTech é uma solução integrada de gestão agrícola inteligente, projetada para monitorar plantações e safras por meio de sensores IoT, análise de dados ambientais, automação de irrigação e controle de produtividade. O sistema conecta o campo à gestão, possibilitando decisões em tempo real baseadas em dados precisos e inovadores.

## Escopo do Sistema

- Backend Java (Spring Boot) para gerenciamento agrícola, usuários e sensores.
- Módulo .NET (C#) para integração com dispositivos, segurança e dashboards.
- Banco de Dados Oracle Cloud para persistência, utilizando Entity Framework Core para mapeamento e migrações.
- Camada IoT com ESP32 para coleta de dados ambientais, integrados via MQTT ou HTTP REST.
- Aplicativo Mobile com TypeScript/Ionic para produtores e técnicos.
- Módulo de IA Oracle para análise preditiva e alertas.
- Integração dos sensores IoT diretamente ao Oracle APEX, ampliando a capacidade de visualização, análise e tomada de decisão dentro da plataforma APEX.

## Melhorias Recentes e Novas Funcionalidades

- **Integração IoT via Oracle APEX:** Implantamos um fluxo para captura de dados ambientais provenientes de sensores ESP32, utilizando protocolos MQTT e HTTP REST para ingestão dos dados. Os dados são recebidos e armazenados no Oracle APEX, que agora funciona também como dashboard e ferramenta analítica interativa para os dados coletados no campo.
- **Fluxos Node-RED para automação:** Desenvolvemos e configuramos fluxos para simulação e captura real dos sensores via Node-RED, facilitando testes, monitoramento e integração contínua com a plataforma backend.
- **Conectividade IoT robusta e escalável:** Uso integrado de MQTT para comunicação em tempo real entre sensores e backend, garantindo alta disponibilidade e confiabilidade dos dados ambientais.
- **Dashboard APEX e analytics aprimorados:** Implementação de dashboards dinâmicos e alertas baseados em dados coletados, com aplicação prática de modelos preditivos com Oracle AI Services.
- **Documentação atualizada:** Organização e consolidação da documentação para melhor compreensão, instalação, configuração e uso do sistema AgroTech, incluindo uso de Swagger/OpenAPI, Docker, e instruções de deploy.

## Integração com Oracle APEX

- Configuração RESTful para consumo de dados do AgroTech.
- Upload e armazenamento de dados ambientais do campo.
- Visualização em tempo real e relatórios históricos.
- Alertas inteligentes baseados em inteligência artificial.
- Modelo de integração flexível com uso de POST via API para recepção dos dados via MQTT intermediado pelo Node-RED.

## Estrutura do Projeto

- Backend Java 21 com Spring Boot 3 em arquitetura em camadas clara.
- Frontend com ASP.NET Core MVC, Mobile Ionic e dashboards web.
- Banco de dados Oracle e PostgreSQL para diferentes ambientes com pipelines de CI/CD.
- Camada IoT com sensores ESP32 e biblioteca MQTT para comunicação.
- Pipeline de integração contínua com Docker e Oracle Cloud Infrastructure.

## Vídeo de Apresentação do Projeto

[Apresentação do Projeto AgroTech no YouTube](https://youtu.be/YdZeIElCnHE?si=0_lcg0uesOWPJqZ3)
