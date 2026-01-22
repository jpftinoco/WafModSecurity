# Projeto 01: Implementação de WAF com ModSecurity e OWASP CRS

## Descrição do Projeto
Este repositório documenta a implementação de um Web Application Firewall (WAF) utilizando ModSecurity e o conjunto de regras OWASP Core Rule Set (CRS). O objetivo principal foi estabelecer uma camada de segurança perimetral para mitigar ataques comuns da camada de aplicação (Camada 7).

A infraestrutura foi provisionada em uma instância Ubuntu no Google Cloud Platform (GCP), utilizando Docker para isolamento e orquestração dos serviços.

## Arquitetura e Tecnologias
* **Provedor de Cloud:** Google Cloud Platform (Compute Engine)
* **Orquestração:** Docker e Docker Compose
* **Servidor Web / Proxy Reverso:** Nginx
* **Engine de Segurança:** ModSecurity v3
* **Regras Base:** OWASP CRS (Core Rule Set)
* **Aplicação Alvo:** OWASP Juice Shop (Ambiente de testes vulnerável)

## Cenário de Teste: Prevenção de SQL Injection
Para validar a eficácia do WAF, foi realizado um teste de Prova de Conceito (PoC) simulando uma tentativa de SQL Injection.

### Comando de Ataque (via cURL)
O comando abaixo simula uma injeção de código SQL para bypass de autenticação:
`curl -I "http://<IP_DA_VM>/index.php?id=%27%20OR%201=1--"`

### Resultado do Bloqueio
Ao identificar o padrão malicioso nas regras do OWASP CRS, o WAF interrompeu a conexão imediatamente:
`curl: (56) Recv failure: Connection reset by peer`

Este comportamento confirma que o ModSecurity está operando em modo de prevenção ativa (SecRuleEngine On), impedindo que a requisição chegue ao servidor de aplicação.

## Desafios de Implementação e Soluções (Troubleshooting)

### 1. Persistência e Permissões em Docker
O contêiner do WAF apresentou falhas críticas ao tentar gravar logs de auditoria em volumes montados como "read-only". A solução envolveu o ajuste do mapeamento de volumes no arquivo docker-compose.yml e a adequação das permissões no host (Linux) para permitir a escrita dos logs de segurança.

### 2. ModSecurity Tuning
Ajuste fino das variáveis de ambiente no Docker para garantir que o engine estivesse em modo de bloqueio imediato, em vez de apenas modo de detecção (Log-only), garantindo a proteção em tempo real.

## Como Executar
1. Certifique-se de ter Docker e Docker Compose instalados.
2. Clone o repositório.
3. Execute o comando: `docker-compose up -d`.
4. Monitore os logs com: `tail -f logs/error.log`.
