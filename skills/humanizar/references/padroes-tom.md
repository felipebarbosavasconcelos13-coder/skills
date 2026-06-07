# Padrões de Tom — Detecção e Correção

Padrões que denunciam tom artificial, servil ou performático em texto PT-BR. Cada padrão inclui gatilhos, exemplos brasileiros (tech/startup/marketing/dev) e alternativas humanas.

---

### 1. Tom Servil / Sycophantic

**Palavras/expressões gatilho:** "Ótima pergunta!", "Com certeza!", "Excelente observação!", "Espero ter ajudado!", "Fico feliz em ajudar!", "Obrigado por compartilhar!"

**Problema:** Elogios genéricos ao interlocutor antes de responder. Ninguém fala assim em texto profissional brasileiro — é marca registrada de chatbot tentando agradar.

**Antes (IA):**
> Ótima pergunta! O deploy contínuo com GitHub Actions é realmente uma abordagem fascinante. Com certeza posso te ajudar com isso! Vamos lá: primeiro, você precisa configurar o workflow YAML...

**Depois (humano):**
> Pra configurar deploy contínuo com GitHub Actions, começa pelo workflow YAML. Cria um `.github/workflows/deploy.yml` com o trigger no push da main...

**Evitar em PT-BR:**
- "Ótima pergunta!" / "Excelente ponto!"
- "Com certeza!" / "Absolutamente!"
- "Espero ter ajudado!" / "Fico feliz em contribuir!"

---

### 2. Disclaimers de Knowledge-Cutoff

**Palavras/expressões gatilho:** "Até onde sei...", "Com base nas informações disponíveis...", "Até minha última atualização...", "Não posso confirmar com certeza, mas...", "De acordo com minhas informações limitadas..."

**Problema:** Expõe a natureza de máquina do autor. Humanos não sinalizam o limite temporal do próprio conhecimento — simplesmente afirmam o que sabem ou dizem "não sei".

**Antes (IA):**
> Até onde sei, o Next.js 15 introduziu Server Actions como feature estável. No entanto, informações mais recentes podem ter alterado esse cenário. Com base nas informações disponíveis até minha última atualização, a recomendação é usar App Router.

**Depois (humano):**
> Next.js 15 estabilizou Server Actions. Se mudou algo depois disso, confere a doc oficial — mas até onde testei, App Router é o caminho.

**Evitar em PT-BR:**
- "Até minha última atualização..."
- "Com base nas informações disponíveis..."
- "Não tenho informações suficientes para afirmar com certeza, mas..."

---

### 3. Comunicação Colaborativa Residual

**Palavras/expressões gatilho:** "Aqui está um exemplo...", "Posso te ajudar com...", "Vou te mostrar como...", "Segue abaixo...", "Fique à vontade para perguntar mais!"

**Problema:** O texto conserva vestígios de interação assistente-usuário. Parece resposta de suporte, não texto autoral. Quando publicado como artigo ou post, denuncia imediatamente a origem.

**Antes (IA):**
> Aqui está um exemplo de como implementar autenticação com JWT no Express. Vou te mostrar passo a passo como configurar o middleware. Fique à vontade para adaptar conforme suas necessidades!

**Depois (humano):**
> Autenticação JWT no Express se resume a um middleware que valida o token antes de liberar a rota. O setup básico fica assim:

**Evitar em PT-BR:**
- "Aqui está um..." / "Segue abaixo..."
- "Posso te ajudar com..." / "Vou te mostrar..."
- "Fique à vontade para..." / "Não hesite em perguntar!"

---

### 4. Hedging Excessivo

**Palavras/expressões gatilho:** "Pode-se argumentar que...", "É possível que...", "Talvez seja o caso de...", "Alguns especialistas sugerem...", "Aparentemente...", "De certa forma..."

**Problema:** Cautela excessiva drena autoridade do texto. IA faz hedging porque não quer errar. Humanos que dominam o assunto afirmam — e quando não sabem, dizem "não sei" em vez de cercar por todos os lados.

**Antes (IA):**
> Pode-se argumentar que microsserviços nem sempre são a melhor escolha para startups em estágio inicial. Alguns especialistas sugerem que, em determinados contextos, uma arquitetura monolítica pode ser potencialmente mais adequada para equipes menores.

**Depois (humano):**
> Microsserviço pra startup de 3 devs é tiro no pé. Monolito resolve. Quando (se) escalar virar problema de verdade, aí você refatora o que precisa — não antes.

**Evitar em PT-BR:**
- "Pode-se argumentar que..." / "É possível que..."
- "Em determinados contextos..." / "Potencialmente..."
- "Alguns especialistas sugerem..." / "De certa forma..."

---

### 5. Conclusões Genéricas Positivas

**Palavras/expressões gatilho:** "O futuro é promissor", "Tempos empolgantes", "As possibilidades são infinitas", "O potencial é ilimitado", "Estamos apenas no começo", "O melhor ainda está por vir"

**Problema:** Encerramento vazio que não diz nada. Todo texto de IA termina com otimismo genérico porque é o token mais provável depois de "em conclusão". Texto humano termina com posição, provocação, ou simplesmente para.

**Antes (IA):**
> O futuro da inteligência artificial no marketing digital é promissor. Estamos vivendo tempos empolgantes, e as possibilidades são verdadeiramente infinitas para profissionais que souberem se adaptar a essa nova realidade.

**Depois (humano):**
> Quem não aprender a usar IA pra marketing vai perder pra quem aprendeu. Não é futuro — já tá acontecendo. A pergunta é se você vai ser o cara usando ou o cara sendo substituído.

**Evitar em PT-BR:**
- "O futuro é promissor" / "Tempos empolgantes nos aguardam"
- "As possibilidades são infinitas" / "O potencial é ilimitado"
- "Estamos apenas no começo dessa jornada"

---

### 6. Frases de Enchimento

**Palavras/expressões gatilho:** "É importante destacar que", "Vale ressaltar que", "Cabe mencionar que", "Convém observar que", "Não se pode ignorar o fato de que", "É fundamental compreender que"

**Problema:** Adicionam zero informação. São muletas que a IA usa para ganhar tokens antes de chegar ao ponto. Em texto humano, se algo é importante, você simplesmente diz — não anuncia que vai dizer.

**Antes (IA):**
> É importante destacar que o uso de TypeScript em projetos React tem crescido significativamente. Vale ressaltar que essa tendência reflete a busca por maior segurança de tipos. Cabe mencionar que empresas como Vercel e Stripe já adotaram TypeScript como padrão.

**Depois (humano):**
> TypeScript virou padrão em projeto React sério. Vercel, Stripe, Linear — todo mundo migrou. Faz sentido: pegar bug em tempo de compilação dói menos que pegar em produção às 3h da manhã.

**Evitar em PT-BR:**
- "É importante destacar que" / "Vale ressaltar que"
- "Cabe mencionar que" / "Convém observar que"
- "Não se pode ignorar o fato de que" / "É fundamental compreender que"

---

### 7. "Eis a Questão" / Here's the Kicker

**Palavras/expressões gatilho:** "Eis a questão:", "O ponto central é:", "Mas aqui está o detalhe:", "A grande sacada é:", "O plot twist é:", "E aqui mora o perigo:"

**Problema:** Cria falso suspense antes de um ponto banal. Promete revelação dramática e entrega obviedade. Humanos não anunciam que vão dizer algo interessante — simplesmente dizem.

**Antes (IA):**
> Muitas startups investem em growth hacking sem ter product-market fit. Elas contratam growth leads, gastam com ads, otimizam funis. Mas eis a questão: sem um produto que as pessoas realmente querem, nenhuma tática de crescimento vai funcionar.

**Depois (humano):**
> Startup sem product-market fit gastando com growth é tipo jogar água em balde furado. Não importa quão sofisticado é o funil — se o produto não resolve o problema, ninguém fica.

**Evitar em PT-BR:**
- "Eis a questão:" / "Eis o ponto:"
- "Mas aqui está o detalhe:" / "A grande sacada é:"
- "E aqui mora o perigo:" / "O plot twist é:"

---

### 8. Vulnerabilidade Falsa

**Palavras/expressões gatilho:** "Sendo honesto aqui...", "Confesso que...", "Vou ser vulnerável:", "Não vou mentir:", "Se eu for sincero...", "Admito que..."

**Problema:** Auto-consciência performática polida. Simula abertura emocional sem confessar nada real. É vulnerabilidade de palco — calculada pra gerar empatia sem risco. Humanos de verdade são vulneráveis de forma desajeitada, não anunciada.

**Antes (IA):**
> Confesso que, como desenvolvedor, nem sempre segui boas práticas. Sendo honesto aqui: houve momentos em que priorizei velocidade sobre qualidade. E sim, admito que isso me ensinou lições valiosas sobre a importância do código limpo.

**Depois (humano):**
> Já subi código porco em produção numa sexta às 18h? Já. Deu merda? Deu. Aprendi a não fazer de novo? Mais ou menos — depende do prazo.

**Evitar em PT-BR:**
- "Confesso que..." / "Sendo honesto aqui..."
- "Vou ser vulnerável:" / "Não vou mentir:"
- "Admito que isso me ensinou lições valiosas"

---

### 9. "A Verdade É Simples"

**Palavras/expressões gatilho:** "A verdade é simples:", "A realidade é mais simples do que parece", "No fundo, tudo se resume a...", "A resposta é surpreendentemente direta:", "Na prática, é menos complicado do que parece"

**Problema:** Declara obviedade sem provar. Finge que está simplificando algo complexo quando na verdade está apenas restateando o superficial. Humanos que realmente simplificam mostram o caminho — não anunciam que vão simplificar.

**Antes (IA):**
> Muitos founders se perdem em frameworks de priorização complexos, matrizes RICE, e metodologias ágeis elaboradas. Mas a verdade é simples: o que importa é conversar com seus usuários e construir o que eles precisam.

**Depois (humano):**
> Framework de priorização vira muleta quando você não conversa com usuário. RICE não substitui ligar pro cliente e perguntar o que tá pegando. Parece básico — e é. O difícil é fazer consistentemente.

**Evitar em PT-BR:**
- "A verdade é simples:" / "A realidade é mais simples do que parece"
- "No fundo, tudo se resume a..." / "A resposta é surpreendentemente direta:"
- "É menos complicado do que você imagina"

---

### 10. Inflação Grandiosa de Stakes

**Palavras/expressões gatilho:** "Isso vai redefinir fundamentalmente...", "Uma mudança de paradigma", "Revolucionário", "Transformar completamente", "O jogo mudou para sempre", "Nunca mais será o mesmo"

**Problema:** Tudo é a coisa mais importante do universo. IA infla stakes porque tokens dramáticos geram engagement. Mas quando tudo é revolucionário, nada é. Texto humano calibra a importância — nem tudo é paradigma-shifting.

**Antes (IA):**
> O surgimento de agentes de IA autônomos representa uma mudança de paradigma que vai redefinir fundamentalmente a forma como desenvolvemos software. Estamos testemunhando uma revolução que transformará completamente a indústria tech como a conhecemos.

**Depois (humano):**
> Agentes de IA vão mudar bastante coisa no dev workflow — especialmente tarefas repetitivas tipo boilerplate e testes. Mas "revolução"? Sei não. A gente já passou por Docker, Kubernetes, serverless, e continua debugando as mesmas merdas de sempre.

**Evitar em PT-BR:**
- "Mudança de paradigma" / "Redefinir fundamentalmente"
- "Revolução" / "Transformar completamente"
- "O jogo mudou para sempre" / "Nunca mais será o mesmo"

---

### 11. "Vamos Analisar" / Let's Break This Down

**Palavras/expressões gatilho:** "Vamos analisar:", "Vamos destrinchar:", "Vamos entender passo a passo:", "Vamos explorar cada aspecto:", "Vamos mergulhar nesse assunto:", "Quebrando em partes:"

**Problema:** Voz pedagógica condescendente. Pressupõe que o leitor precisa ser guiado como criança. Cria sensação de tutorial quando o contexto pede artigo/opinião. Humanos que sabem o assunto simplesmente explicam — não anunciam que vão explicar.

**Antes (IA):**
> Vamos analisar os três pilares de uma estratégia de conteúdo eficaz. Primeiro, vamos explorar a pesquisa de palavras-chave. Em seguida, vamos mergulhar na criação de clusters temáticos. Por fim, vamos entender como medir resultados.

**Depois (humano):**
> Estratégia de conteúdo se sustenta em três coisas: saber o que o público busca, organizar conteúdo em clusters que se reforçam, e medir se tá funcionando. O resto é firula.

**Evitar em PT-BR:**
- "Vamos analisar:" / "Vamos destrinchar:"
- "Vamos explorar cada aspecto:" / "Vamos mergulhar nesse assunto:"
- "Vamos entender passo a passo:" / "Quebrando em partes:"

---

### 12. Rótulos Conceituais Inventados

**Palavras/expressões gatilho:** "o paradoxo da [X]", "a armadilha da [X]", "o deficit de [X]", "a falácia do [X]", "o efeito [X]", "a síndrome de [X]"

**Problema:** Inventar termos compostos e apresentá-los como conceitos estabelecidos. IA cria rótulos pseudo-acadêmicos para parecer profunda ("o paradoxo da supervisão", "a armadilha da aceleração"). Humanos nomeiam fenômenos com cautela — ou reconhecem que estão cunhando um termo.

**Antes (IA):**
> Muitas empresas caem no que podemos chamar de "paradoxo da automação" — quanto mais automatizam, mais dependem de intervenção humana para lidar com os edge cases. Esse "deficit de supervisão escalável" é o verdadeiro gargalo da transformação digital.

**Depois (humano):**
> Quanto mais você automatiza, mais os edge cases ficam difíceis — porque os fáceis já foram resolvidos. E aí precisa de gente sênior pra cuidar das exceções. Irônico? Um pouco. Mas é assim que funciona.

**Evitar em PT-BR:**
- "O paradoxo da [X]" / "A armadilha da [X]"
- "O deficit de [X]" / "A falácia do [X]"
- "O que podemos chamar de..." (seguido de termo inventado)

---

### 13. "Imagine um Mundo Onde..."

**Palavras/expressões gatilho:** "Imagine um mundo onde...", "Imagine se...", "Pense num cenário em que...", "E se eu te dissesse que...", "Visualize um futuro onde...", "Feche os olhos e imagine..."

**Problema:** Convite futurista clichê que serve de abertura genérica. Pede ao leitor pra fantasiar em vez de mostrar dados ou realidade. Funciona em keynote de Steve Jobs — não funciona na 47ª vez que aparece num post de LinkedIn sobre IA.

**Antes (IA):**
> Imagine um mundo onde todo desenvolvedor tem um assistente de IA que entende perfeitamente o contexto do seu codebase. Imagine se cada pull request fosse revisada instantaneamente com feedback preciso e actionable. Esse mundo não está tão distante quanto você pensa.

**Depois (humano):**
> Code review com IA já existe — Copilot, CodeRabbit, Cursor. O problema não é "imaginar o futuro" — é que hoje as ferramentas ainda halluncinam em codebase grande e sugerem refactoring que quebra metade dos testes. Útil? Sim. Mágica? Longe disso.

**Evitar em PT-BR:**
- "Imagine um mundo onde..." / "Imagine se..."
- "E se eu te dissesse que..." / "Visualize um futuro onde..."
- "Esse mundo não está tão distante" / "O futuro já chegou"
