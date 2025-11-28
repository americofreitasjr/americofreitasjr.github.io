# Fim da Escala Forçada: O Fim do NLB como Intermediário entre API Gateway e ALB

*Publicado em 21 de novembro de 2025*

[Read in English 🇺🇸](./en.md)

![Diagrama de arquitetura mostrando a simplificação da integração do API Gateway com o ALB](https://raw.githubusercontent.com/americofreitasjr/americofreitasjr.github.io/main/public/20251121-api-gateway-alb/blog/img.png)

Por anos, uma das pequenas "dores de cabeça" na arquitetura de soluções na AWS era a necessidade de expor serviços internos, balanceados por um Application Load Balancer (ALB) privado, através do API Gateway. A solução, embora funcional, sempre pareceu um desvio: era mandatório posicionar um Network Load Balancer (NLB) como intermediário.

`API Gateway -> VPC Link -> NLB -> ALB -> Microsserviços`

Esse NLB existia por uma única razão: o VPC Link do API Gateway historicamente só se integrava a ele. Funcionava, mas adicionava uma camada de infraestrutura que, na maioria dos cenários, se resumia a um repassador de tráfego, trazendo consigo custos, latência e complexidade operacional.

Recentemente, a AWS removeu essa barreira. Em um anúncio que foi celebrado por arquitetos de nuvem em todos os lugares, **o API Gateway REST agora suporta integração privada direta com o Application Load Balancer via VPC Link.**

Isso representa uma mudança fundamental na forma como projetamos topologias de rede para APIs privadas.

## Análise Técnica: O Impacto da Remoção do NLB

Para entender a relevância dessa atualização, vamos dissecar as três principais vantagens diretas.

### 1. Simplificação da Topologia e Redução da Superfície de Gerenciamento

Cada componente em uma arquitetura é um ponto de configuração, monitoramento e potencial falha. O NLB intermediário exigia seu próprio Target Group, Health Checks, logs e métricas no CloudWatch. Embora simples, era mais um elemento na cadeia.

A nova arquitetura é linear e intuitivamente mais limpa:

`API Gateway -> VPC Link -> ALB -> Microsserviços`

Isso não apenas simplifica o diagrama da solução, mas também centraliza a lógica de roteamento avançada (baseada em path, host, headers) diretamente no ALB, que é a ferramenta certa para esse trabalho, sem a necessidade de configurações adicionais no NLB.

### 2. Redução de Custo e Latência

Vamos ser diretos: o NLB, como qualquer serviço gerenciado, tem um custo. Ele é composto por uma taxa horária e uma taxa de processamento de dados (LCU - Load Balancer Capacity Unit). Para APIs com alto volume de tráfego, esse custo não era desprezível.

Além do custo financeiro, há o custo de performance. Cada *hop* em uma rede introduz latência. A remoção do NLB elimina um salto, resultando em uma pequena, mas mensurável, redução no tempo de resposta ponta a ponta. Em sistemas que dependem de baixa latência, cada milissegundo conta.

### 3. Flexibilidade na Arquitetura de Microsserviços

O ALB é uma ferramenta muito mais rica em funcionalidades de roteamento do que o NLB. Ele opera na camada 7 (aplicação), permitindo regras complexas. Ao conectar o API Gateway diretamente a ele, ganhamos agilidade.

Imagine um cenário com múltiplos serviços (ex: `/users`, `/orders`, `/products`) rodando em diferentes clusters de ECS ou EKS, todos servidos pelo mesmo ALB. Com a integração direta, o API Gateway pode simplesmente encaminhar todo o tráfego para o ALB, que então utiliza suas regras internas para direcionar a requisição ao serviço correto. A lógica fica consolidada onde deveria estar.

## Considerações sobre a Migração: É Hora de Desligar meu NLB?

Como toda decisão de engenharia, a resposta é: **depende do contexto.**

*   **Cenário Ideal para Migração:** Você possui uma arquitetura onde o NLB serve exclusivamente como um intermediário para um ou mais ALBs. Nesse caso, a migração é altamente recomendada e o ganho é claro e imediato.

*   **Cenários que Exigem Cautela:** Se o seu NLB atende a outros propósitos, a decisão é mais complexa. Por exemplo, se você depende dos endereços IP estáticos que um NLB pode fornecer para integrações com sistemas legados, ou se outros serviços (não-HTTP) consomem o NLB diretamente, ele ainda tem seu lugar na arquitetura. Nesses casos, a migração pode ser parcial ou simplesmente não valer o esforço.

## Conclusão: Uma Evolução Natural e Bem-vinda

A integração direta entre o API Gateway e o ALB privado não é apenas uma nova *feature*. É a correção de uma antiga fricção arquitetônica, demonstrando que a AWS continua atenta às necessidades práticas da comunidade de desenvolvedores.

É um passo em direção a arquiteturas mais simples, eficientes e econômicas — três pilares que todos nós, engenheiros e arquitetos, buscamos incessantemente.

---
**Referência Oficial:** [AWS Compute Blog - Build scalable and resilient REST APIs using Amazon API Gateway private integration with Application Load Balancer](https://aws.amazon.com/pt/blogs/compute/build-scalable-rest-apis-using-amazon-api-gateway-private-integration-with-application-load-balancer/)
