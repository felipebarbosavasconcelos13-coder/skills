# Padrões Exclusivos do Português Brasileiro

Padrões de texto de IA que **só existem em PT-BR** — não têm equivalente na skill humanizer original em inglês. São marcadores culturais e linguísticos que denunciam texto gerado por máquina especificamente no contexto brasileiro.

Estes padrões exploram vícios do português corporativo, jurídico e acadêmico brasileiro que LLMs absorveram de seus dados de treinamento e reproduzem de forma desproporcional.

---

## 1. Gerundismo

### Gerundismo Corporativo

**Palavras/expressões gatilho:** "vou estar enviando", "estaremos realizando", "vai estar recebendo", "iremos estar providenciando", "vamos estar agendando"

**Problema:** LLMs absorveram o gerundismo de e-mails corporativos e scripts de telemarketing brasileiros. Nenhum humano escreve assim voluntariamente — é padrão de SAC e call center que virou piada nacional. Quando aparece em texto "natural", grita automação.

**Antes (IA):**
> Vou estar enviando o relatório de métricas do Q2 para validação. A equipe de produto vai estar realizando a análise de impacto e estaremos agendando uma call para alinhamento na próxima semana.

**Depois (humano):**
> Mando o relatório de métricas do Q2 até sexta. O time de produto analisa o impacto e a gente marca uma call semana que vem pra alinhar.

**Evitar em PT-BR:**
- "Vou estar enviando"
- "Estaremos realizando"
- "Vai estar recebendo"
- "Iremos estar providenciando"
- "Vamos estar disponibilizando"

**Alternativas naturais:**
- "Vou enviar" / "Envio amanhã"
- "Vamos fazer" / "A gente faz"
- "Você recebe" / "Chega até sexta"
- "Providencio" / "Resolvo"

---

## 2. Conectivos Arcaicos Fora de Contexto

### Latinismo Jurídico em Texto Informal

**Palavras/expressões gatilho:** "ademais", "outrossim", "destarte", "não obstante", "doravante", "nesse diapasão", "mister se faz", "em última análise", "no bojo de", "ab initio"

**Problema:** Essas palavras pertencem ao registro de contratos, decisões judiciais e regulamentos universitários. LLMs as usam em posts de blog, e-mails de produto e copy de landing page porque foram treinados em muito texto jurídico brasileiro (que é desproporcionalmente formal comparado a outros idiomas). Brasileiro nenhum usa "outrossim" num Slack.

**Antes (IA):**
> A implementação do novo sistema de pagamentos trouxe resultados expressivos. Ademais, a taxa de churn reduziu 15%. Outrossim, o NPS subiu 12 pontos. Destarte, pode-se concluir que a estratégia foi bem-sucedida.

**Depois (humano):**
> O novo sistema de pagamentos deu resultado: churn caiu 15%, NPS subiu 12 pontos. Funcionou.

**Evitar em PT-BR:**
- "Ademais" (fora de petições judiciais)
- "Outrossim" (em qualquer contexto que não seja um contrato)
- "Destarte" (ninguém fala isso desde 1920)
- "Nesse diapasão" (só juiz usa)
- "Mister se faz" (linguagem de despacho)
- "Doravante" (até em contratos tá caindo em desuso)

**Alternativas naturais:**
- "Ademais" → "Além disso" / "E ainda" / simplesmente vírgula
- "Outrossim" → "Também" / "E" / (cortar — a frase seguinte já diz)
- "Destarte" → "Então" / "Por isso" / "Resultado:"
- "Não obstante" → "Mesmo assim" / "Apesar disso" / "Mas"
- "Doravante" → "A partir de agora" / "De agora em diante"

---

## 3. Aberturas Genéricas Estilo ENEM

### Dissertação de Vestibular Disfarçada

**Palavras/expressões gatilho:** "Em um mundo cada vez mais...", "No cenário atual...", "Na contemporaneidade...", "É inegável que...", "Diante do exposto...", "No contexto de...", "Em meio a um cenário de..."

**Problema:** LLMs reproduzem a estrutura da redação nota 1000 do ENEM — abertura genérica que contextualiza o tema de forma ampla antes de dizer qualquer coisa específica. Todo brasileiro reconhece esse padrão porque escreveu assim no vestibular. Em texto profissional, é sinal claro de que ninguém pensou antes de escrever.

**Antes (IA):**
> Em um mundo cada vez mais digitalizado, as fintechs brasileiras vêm desempenhando um papel fundamental na democratização do acesso a serviços financeiros. No cenário atual, é inegável que a tecnologia transformou a maneira como lidamos com dinheiro.

**Depois (humano):**
> 70 milhões de brasileiros não tinham conta bancária em 2019. Hoje têm — e a maioria abriu pelo celular. Fintechs fizeram em 5 anos o que bancos não fizeram em 50.

**Evitar em PT-BR:**
- "Em um mundo cada vez mais [adjetivo]..."
- "No cenário atual..."
- "Na contemporaneidade..."
- "É inegável que..."
- "No contexto de [tema genérico]..."
- "Diante de um cenário de..."
- "É sabido que..."
- "Nos dias atuais..."

**Alternativas naturais:**
- Começar com dado concreto: "42% dos..." / "Em 2024..."
- Começar com afirmação direta: "Fintechs mudaram o jogo."
- Começar com pergunta real: "Quantos brasileiros abriram conta digital esse ano?"
- Começar com exemplo: "Maria, 23, de Recife, nunca pisou num banco."
- Começar pelo meio: pular o contexto e ir direto ao ponto

---

## 4. Hedging Burocrático

### Marcadores de Importância Artificial

**Palavras/expressões gatilho:** "Vale ressaltar que...", "É importante destacar que...", "Cumpre salientar que...", "Faz-se necessário...", "É fundamental observar que...", "Convém mencionar que...", "Importa registrar que...", "Merece atenção o fato de que..."

**Problema:** Essas expressões existem para preencher espaço sem dizer nada. São muletas de quem precisa parecer que está dizendo algo importante sem comprometer-se com a afirmação. LLMs usam MUITO porque o treinamento inclui toneladas de texto burocrático brasileiro (diários oficiais, pareceres, relatórios corporativos). Brasileiro real, quando quer ressaltar algo, simplesmente diz.

**Antes (IA):**
> Vale ressaltar que a taxa de conversão do funil apresentou crescimento significativo. É importante destacar que esse resultado está diretamente relacionado à implementação das novas landing pages. Cumpre salientar que a equipe de growth executou 14 testes A/B no período.

**Depois (humano):**
> Conversão do funil subiu 23%. O que mudou? Landing pages novas + 14 testes A/B que o time de growth rodou no trimestre.

**Evitar em PT-BR:**
- "Vale ressaltar que..."
- "É importante destacar que..."
- "Cumpre salientar que..."
- "Faz-se necessário observar que..."
- "É fundamental que se reconheça..."
- "Convém mencionar que..."
- "Importa registrar que..."
- "Merece destaque o fato de que..."

**Alternativas naturais:**
- Cortar a expressão inteira e começar pela informação: "A taxa de conversão subiu 23%."
- Se precisa enfatizar: usar posição na frase (colocar no início) ou itálico
- Se precisa transição: "Detalhe importante:" / "O ponto aqui é:" / "Olha isso:"
- Para tom mais informal: "E tem mais:" / "O melhor:" / "Agora, atenção:"

---

## 5. Formalidade Deslocada

### Registro de Ofício em Contexto de Startup

**Palavras/expressões gatilho:** "No que tange a", "Tendo em vista que", "O referido", "Conforme mencionado anteriormente", "O supracitado", "No tocante a", "Em face do exposto", "Haja vista que"

**Problema:** LLMs confundem "escrever bem" com "escrever formal". Em PT-BR, o registro formal extremo pertence a documentos oficiais (ofícios, memorandos, atas). Quando aparece em email de produto, post de blog tech, ou comunicação interna de startup, parece que um robô leu o Manual de Redação da Presidência da República e achou que serve pra tudo.

**Antes (IA):**
> No que tange à implementação do novo design system, tendo em vista que a equipe de front-end sinalizou gargalos, faz-se necessário priorizar a refatoração do referido sistema de componentes. Conforme mencionado anteriormente, o supracitado projeto tem deadline no Q3.

**Depois (humano):**
> Sobre o design system: o front sinalizou gargalo. Precisamos priorizar a refatoração dos componentes antes do deadline do Q3.

**Evitar em PT-BR:**
- "No que tange a" (fora de parecer jurídico)
- "Tendo em vista que" (fora de justificativa formal)
- "O referido" / "O supracitado" (fora de processo judicial)
- "Conforme mencionado anteriormente" (redundante — se mencionou, o leitor sabe)
- "Em face do exposto" (conclusão de parecer)
- "Haja vista que" (pedante em qualquer contexto informal)
- "No tocante a" (burocracia pura)

**Alternativas naturais:**
- "No que tange a" → "Sobre" / "Quanto a"
- "Tendo em vista que" → "Como" / "Já que" / "Porque"
- "O referido" → "Esse" / "O" / (cortar — o contexto já diz)
- "Conforme mencionado anteriormente" → cortar inteiro, ou "Como falei"
- "Em face do exposto" → "Então" / "Resumindo"
- "Haja vista que" → "Já que" / "Porque"

---

## 6. Officialese Brasileiro

### Linguagem Jurídica/Burocrática Fora do Contexto Legal

**Palavras/expressões gatilho:** "Segue em anexo para os devidos fins", "Venho por meio deste", "Solicito a gentileza de", "Segue para conhecimento e providências", "Informo para os devidos fins", "Encaminho o presente para apreciação", "Trata o presente de"

**Problema:** O "officialese" brasileiro é um dialeto próprio — linguagem de ofício, memorando e despacho que LLMs internalizaram de forma massiva porque a administração pública brasileira produz volumes absurdos de texto nesse registro. Quando aparece fora do contexto público/legal, denuncia geração automática. Nenhum PM de startup escreve "venho por meio deste" num Notion.

**Antes (IA):**
> Venho por meio deste comunicar que a feature de onboarding encontra-se em fase final de implementação. Solicito a gentileza de agendar a revisão de código para os devidos fins de validação. Segue em anexo o documento de especificação para conhecimento e eventuais providências.

**Depois (humano):**
> Onboarding tá quase pronto. Preciso de code review — marco pra quando? Spec em anexo.

**Evitar em PT-BR:**
- "Venho por meio deste [comunicar/informar/solicitar]"
- "Segue em anexo para os devidos fins"
- "Solicito a gentileza de"
- "Para conhecimento e providências"
- "Informo para os devidos fins que"
- "Trata o presente [email/documento] de"
- "Sirvo-me do presente para"
- "Encaminho para apreciação superior"

**Alternativas naturais:**
- "Venho por meio deste informar" → "Atualizando:" / "Novidade:" / ir direto
- "Segue em anexo" → "Tá anexo" / "Mandei junto" / "Arquivo aqui:"
- "Solicito a gentileza" → "Pode [fazer X]?" / "Preciso que"
- "Para conhecimento" → "Pra vocês saberem:" / "FYI:" / "Contexto:"
- "Para os devidos fins" → cortar (nunca serve pra nada)

---

## 7. Evitação de Verbos Simples

### Substituição de "É/São/Tem" por Eufemismos Rebuscados

**Palavras/expressões gatilho:** "constitui", "configura-se como", "dispõe de", "encontra-se", "figura como", "representa", "serve como", "afigura-se como", "situa-se", "apresenta-se como"

**Problema:** Estudos mostram >10% de queda no uso de copulativas simples ("é", "são", "está") em textos gerados por IA pós-2023. LLMs evitam verbos simples e os substituem por construções rebuscadas — provavelmente porque o RLHF penaliza respostas "simples demais". Em PT-BR, isso cria frases que nenhum brasileiro falaria em voz alta.

**Antes (IA):**
> O Nubank constitui uma das maiores fintechs da América Latina e configura-se como referência em experiência do usuário. A empresa dispõe de mais de 80 milhões de clientes e encontra-se em expansão para novos mercados. Seu modelo de negócios figura como paradigma para startups do setor.

**Depois (humano):**
> O Nubank é uma das maiores fintechs da América Latina e virou referência em UX. Tem mais de 80 milhões de clientes e tá expandindo pra novos mercados. O modelo de negócio deles é o que toda fintech quer copiar.

**Evitar em PT-BR:**
- "constitui [algo]" (quando "é" resolve)
- "configura-se como" (quando "é" resolve)
- "dispõe de" (quando "tem" resolve)
- "encontra-se [em algum estado]" (quando "está" resolve)
- "figura como" (quando "é" resolve)
- "situa-se" (quando "fica" ou "está" resolve)
- "apresenta-se como" (quando "parece" ou "é" resolve)
- "afigura-se como" (ninguém fala isso)

**Alternativas naturais:**
- "constitui uma referência" → "é referência" / "virou referência"
- "dispõe de 80 milhões" → "tem 80 milhões"
- "encontra-se em expansão" → "tá expandindo" / "está crescendo"
- "configura-se como líder" → "é líder" / "lidera"
- "situa-se entre os maiores" → "está entre os maiores" / "é um dos maiores"

---

## 8. Expressões Infladas

### Vocabulário que Brasileiro Não Usa na Fala

**Palavras/expressões gatilho:** "contribui significativamente para", "no âmbito de", "de forma expressiva", "em termos de", "a nível de", "no que diz respeito a", "sob a ótica de", "à luz de", "na esfera de", "potencializar"

**Problema:** LLMs produzem frases que parecem tradução simultânea de jargão corporativo americano filtrado por um manual de redação de 1995. São expressões que aparecem em relatórios anuais e apresentações de PowerPoint, mas que nenhum brasileiro usa quando está explicando algo de verdade. O teste é simples: se você não falaria isso em voz alta numa reunião, não deveria escrever.

**Antes (IA):**
> A estratégia de product-led growth contribui significativamente para a escalabilidade do negócio no âmbito do mercado brasileiro de SaaS. No que diz respeito à aquisição de usuários, a abordagem potencializa os resultados de forma expressiva, sob a ótica da eficiência operacional.

**Depois (humano):**
> PLG funciona bem pra escalar SaaS no Brasil. A aquisição de usuários fica mais barata porque o produto vende sozinho — menos dependência de sales.

**Evitar em PT-BR:**
- "contribui significativamente para" (contribui quanto? diz o número)
- "no âmbito de" (99% das vezes é só "em")
- "de forma expressiva/significativa" (quanto? diz o número)
- "no que diz respeito a" (é "sobre")
- "sob a ótica de" (é "pra" ou "do ponto de vista de")
- "potencializar" (é "melhorar" ou "aumentar" — qual dos dois?)
- "à luz de" (é "considerando" ou "com base em")
- "a nível de" (errado gramaticalmente E vazio semanticamente)
- "na esfera de" (é "em")

**Alternativas naturais:**
- "contribui significativamente" → número concreto: "aumentou 30%" / "reduziu pela metade"
- "no âmbito de" → "em" / "dentro de" / (cortar)
- "potencializar resultados" → "melhorar [métrica específica]" / "aumentar [o quê]"
- "de forma expressiva" → dizer o quanto: "3x mais" / "dobrou"
- "no que diz respeito a" → "sobre" / "quanto a"
- "sob a ótica de" → "pra [quem]" / "do lado de [quem]"

---

## 9. Transições Mecânicas Repetidas

### O Parágrafo que Começa Sempre Igual

**Palavras/expressões gatilho:** "Além disso", "Nesse sentido", "Diante disso", "Em contrapartida", "Por outro lado", "No entanto", "Dessa forma", "Sendo assim", "Nessa perspectiva", "À vista disso"

**Problema:** IA em PT-BR usa transições como muleta estrutural — todo parágrafo começa com um conectivo, criando um ritmo mecânico reconhecível. É o equivalente brasileiro do "Furthermore" / "Moreover" em inglês. Humanos brasileiros variam: às vezes usam transição, às vezes começam direto, às vezes com pergunta, às vezes com exemplo. A repetição de "Além disso... Nesse sentido... Diante disso..." a cada parágrafo é fingerprint de IA.

**Antes (IA):**
> O mercado de SaaS B2B no Brasil cresceu 40% em 2025. **Além disso**, a entrada de novos players internacionais acirrou a competição. **Nesse sentido**, startups brasileiras precisam diferenciação clara. **Diante disso**, estratégias de nicho ganham relevância. **Em contrapartida**, o aumento de competição também valida o mercado. **Dessa forma**, empresas que encontrarem seu posicionamento tendem a prosperar.

**Depois (humano):**
> Mercado de SaaS B2B no Brasil cresceu 40% em 2025. Mais players internacionais entraram — o que é bom e ruim ao mesmo tempo. Bom porque valida o mercado. Ruim porque agora você precisa de diferenciação real, não aquele "somos a plataforma completa" genérico. Quem achar um nicho específico e dominar, ganha. O resto vai brigar por preço.

**Evitar em PT-BR:**
- "Além disso" como abertura de mais de 1 parágrafo no mesmo texto
- "Nesse sentido" sem referência clara ao "sentido"
- "Diante disso" / "Diante do exposto" (dissertação de ENEM)
- "Em contrapartida" quando não há real contrapartida
- "Por outro lado" quando não há dois lados claros
- "Dessa forma" / "Sendo assim" como conclusão automática
- Qualquer padrão de [conectivo] + [afirmação] repetido 3+ vezes seguidas

**Alternativas naturais:**
- Começar com o conteúdo direto, sem transição: "Startups brasileiras precisam se diferenciar."
- Pergunta retórica: "E o que isso significa na prática?"
- Fragmento: "Resultado: nicho vira estratégia, não limitação."
- Exemplo concreto: "A Conta Azul fez isso — focou em MEI e dominou."
- Contraste implícito (sem "por outro lado"): "Só que mais competição também valida o mercado."
- Continuação natural com "E", "Mas", "Só que", "Agora"

---

## 10. Purismo Linguístico Artificial

### Traduzir Estrangeirismos que Brasileiro Usa Naturalmente

**Palavras/expressões gatilho:** "retroalimentação" (feedback), "implantação" (deploy), "rotatividade de clientes" (churn), "corrida/iteração" (sprint), "fluxo de trabalho" (workflow), "partes interessadas" (stakeholders), "entregáveis" (deliverables), "plataforma de dados" (data lake)

**Problema:** LLMs foram treinados com textos acadêmicos e governamentais que evitam estrangeirismos por política editorial. Quando geram texto sobre tech/startup/marketing, traduzem termos que nenhum profissional brasileiro traduz. "Vamos reduzir o churn" é como todo mundo fala. "Vamos reduzir a rotatividade de clientes" soa como tradução de livro técnico de 2003. Forçar tradução de termos naturalizados é um dos sinais mais claros de IA em PT-BR tech.

**Antes (IA):**
> A equipe realizou a implantação do novo microsserviço e obteve retroalimentação positiva das partes interessadas. A rotatividade de clientes diminuiu após a iteração focada em experiência do usuário. O fluxo de trabalho foi otimizado para entregar os entregáveis dentro do prazo da corrida.

**Depois (humano):**
> Time fez o deploy do microsserviço novo e o feedback dos stakeholders foi positivo. Churn caiu depois da sprint focada em UX. Workflow ficou mais redondo pra entregar tudo no prazo.

**Evitar em PT-BR (traduzir quando o termo inglês já é padrão no mercado):**
- "retroalimentação" em vez de "feedback"
- "implantação" em vez de "deploy" (contexto tech)
- "rotatividade de clientes" em vez de "churn"
- "corrida" ou "iteração" em vez de "sprint"
- "partes interessadas" em vez de "stakeholders"
- "entregáveis" em vez de "deliverables"
- "fluxo de trabalho" em vez de "workflow"
- "computação em nuvem" em vez de "cloud" (contexto dev)
- "aprendizado de máquina" em vez de "machine learning" (contexto tech)
- "cadeia de suprimentos" em vez de "supply chain" (contexto startup/ops)

**Alternativas naturais:**
- Usar o termo em inglês como brasileiro usa: "feedback", "deploy", "churn", "sprint"
- Se precisa explicar pra audiência não-tech, usar o inglês + explicação: "churn (perda de clientes)"
- Manter consistência: se usou "deploy" uma vez, não alterna com "implantação"
- Regra de ouro: se o termo aparece em vagas de emprego brasileiras em inglês, use em inglês

---

## Resumo: Checklist Rápido Anti-IA em PT-BR

| # | Padrão | Teste rápido |
|---|---|---|
| 1 | Gerundismo | Tem "vou estar + gerúndio"? |
| 2 | Conectivos arcaicos | Tem "ademais", "outrossim", "destarte" fora de contexto jurídico? |
| 3 | Abertura ENEM | Começa com "Em um mundo cada vez mais..."? |
| 4 | Hedging burocrático | Tem "Vale ressaltar que" ou "Cumpre salientar"? |
| 5 | Formalidade deslocada | Tem "No que tange a" num email de startup? |
| 6 | Officialese | Tem "Venho por meio deste" fora de ofício? |
| 7 | Evitação de verbos simples | "É" virou "constitui" ou "configura-se como"? |
| 8 | Expressões infladas | Tem "contribui significativamente" sem número? |
| 9 | Transições mecânicas | Todo parágrafo começa com conectivo? |
| 10 | Purismo linguístico | Traduziu "feedback", "deploy", "churn"? |

**Se 3+ padrões aparecem no mesmo texto: alta probabilidade de geração por IA.**

---

## Nota sobre Contexto

Estes padrões são calibrados para texto profissional brasileiro nas áreas de:
- Tecnologia (dev, produto, infra)
- Startups e scale-ups
- Marketing digital
- Fintech e SaaS
- Comunicação corporativa moderna

Em contextos onde a formalidade é esperada (petição judicial, artigo acadêmico publicado, comunicação diplomática), alguns desses padrões podem ser aceitáveis. A skill deve considerar o **preset de voz** ativo antes de flaggar.
