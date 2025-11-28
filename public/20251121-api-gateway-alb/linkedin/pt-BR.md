Quem nunca precisou expor um serviço interno via API Gateway e se deparou com o setup:

`API Gateway -> VPC Link -> NLB -> ALB -> Serviço`

Era o caminho padrão, mas sempre com aquela sensação de "precisava mesmo desse NLB aqui?".

A boa notícia: **não precisa mais.**

A AWS finalmente liberou a integração privada direta entre o API Gateway (REST) e o Application Load Balancer (ALB).

**O que isso muda no dia a dia?**

✅ **Menos Complexidade:** Sua arquitetura fica mais limpa e com menos pontos de gerenciamento. Adeus, NLB intermediário.

✅ **Menos Custo:** Um componente a menos significa uma linha a menos na sua fatura da AWS.

✅ **Menos Latência:** Remover um *hop* da rede otimiza o tempo de resposta das suas APIs.

Essa atualização é um presente para quem trabalha com microsserviços e precisa de uma arquitetura interna robusta e eficiente.

**Atenção:** Se a sua arquitetura já está estável e o NLB cumpre outras funções, avalie com calma. Nem toda novidade exige uma migração imediata. O bom senso manda analisar o impacto antes de agir.

Ótimo passo da AWS para tornar o design de APIs privadas mais inteligente.

🔗 **Referência oficial:** https://aws.amazon.com/pt/blogs/compute/build-scalable-rest-apis-using-amazon-api-gateway-private-integration-with-application-load-balancer/

#AWS #CloudArchitect #DevOps #Microservices #API #Networking
