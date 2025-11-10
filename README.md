# [translate:Projeto AgroTech - Arquitetura e Solução] (Atualizado)

## [translate:Objetivo do Projeto]

O [translate:AgroTech é uma solução integrada de gestão agrícola inteligente, projetada para monitorar plantações e safras por meio de sensores IoT, análise de dados ambientais, automação de irrigação e controle de produtividade. O sistema conecta o campo à gestão, possibilitando decisões em tempo real baseadas em dados precisos e inovadores.]

## [translate:Escopo do Sistema]

- [translate:Backend Java (Spring Boot) para gerenciamento agrícola, usuários e sensores.]
- [translate:Módulo .NET (C#) para integração com dispositivos, segurança e dashboards.]
- [translate:Banco de Dados Oracle Cloud para persistência, utilizando Entity Framework Core para mapeamento e migrações.]
- [translate:Camada IoT com ESP32 para coleta de dados ambientais, integrados via MQTT ou HTTP REST.]
- [translate:Aplicativo Mobile com TypeScript/Ionic para produtores e técnicos.]
- [translate:Módulo de IA Oracle para análise preditiva e alertas.]
- [translate:Integração dos sensores IoT diretamente ao Oracle APEX, ampliando a capacidade de visualização, análise e tomada de decisão dentro da plataforma APEX.]

## [translate:Melhorias Recentes e Novas Funcionalidades]

- [translate:Integração IoT via Oracle APEX:] [translate:Implantamos um fluxo para captura de dados ambientais provenientes de sensores ESP32, utilizando protocolos MQTT e HTTP REST para ingestão dos dados. Os dados são recebidos e armazenados no Oracle APEX, que agora funciona também como dashboard e ferramenta analítica interativa para os dados coletados no campo.]
- [translate:Fluxos Node-RED para automação:] [translate:Desenvolvemos e configuramos fluxos para simulação e captura real dos sensores via Node-RED, facilitando testes, monitoramento e integração contínua com a plataforma backend.]
- [translate:Conectividade IoT robusta e escalável:] [translate:Uso integrado de MQTT para comunicação em tempo real entre sensores e backend, garantindo alta disponibilidade e confiabilidade dos dados ambientais.]
- [translate:Dashboard APEX e analytics aprimorados:] [translate:Implementação de dashboards dinâmicos e alertas baseados em dados coletados, com aplicação prática de modelos preditivos com Oracle AI Services.]
- [translate:Documentação atualizada:] [translate:Organização e consolidação da documentação para melhor compreensão, instalação, configuração e uso do sistema AgroTech, incluindo uso de Swagger/OpenAPI, Docker, e instruções de deploy.]

## [translate:Integração com Oracle APEX]

- [translate:Configuração RESTful para consumo de dados do AgroTech.]
- [translate:Upload e armazenamento de dados ambientais do campo.]
- [translate:Visualização em tempo real e relatórios históricos.]
- [translate:Alertas inteligentes baseados em inteligência artificial.]
- [translate:Modelo de integração flexível com uso de POST via API para recepção dos dados via MQTT intermediado pelo Node-RED.]

## [translate:Estrutura do Projeto]

- [translate:Backend Java 21 com Spring Boot 3 em arquitetura em camadas clara.]
- [translate:Frontend com ASP.NET Core MVC, Mobile Ionic e dashboards web.]
- [translate:Banco de dados Oracle e PostgreSQL para diferentes ambientes com pipelines de CI/CD.]
- [translate:Camada IoT com sensores ESP32 e biblioteca MQTT para comunicação.]
- [translate:Pipeline de integração contínua com Docker e Oracle Cloud Infrastructure.]

## [translate:Vídeo de Apresentação do Projeto]

[Apresentação do Projeto AgroTech no YouTube](https://youtu.be/YdZeIElCnHE?si=0_lcg0uesOWPJqZ3)
