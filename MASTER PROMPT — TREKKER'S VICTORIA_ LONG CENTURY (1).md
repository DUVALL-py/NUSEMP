# MASTER PROMPT — TREKKER'S VICTORIA: LONG CENTURY

## 0. IDENTIDADE DO PROJETO

Cria um mod completo para **Victoria 3** chamado:

# **Trekker's Victoria: Long Century**

**Subtítulo:**

> *A lightweight multiplayer overhaul — 1836–1945*

O mod deve ser desenvolvido especificamente para **multiplayer**, com foco em:

- campanhas longas;
- estabilidade;
- baixo consumo de CPU;
- redução de travamentos no late game;
- minimização de desync;
- gameplay histórico/arcade;
- conteúdo suficiente para tornar 1836–1945 interessante;
- código simples e eficiente;
- evitar sistemas desnecessariamente complexos.

A campanha principal deve ser projetada para funcionar de:

**1836 → 1945**

O objetivo não é criar um overhaul gigantesco. O objetivo é criar um **Victoria 3 MP limpo, rápido e divertido**, com sistemas cuidadosamente escolhidos.

---

# 1. REFERÊNCIAS OBRIGATÓRIAS

Usa os seguintes mods como referências/base de estudo:

### Tyrone MP

[https://steamcommunity.com/sharedfiles/filedetails/?id=3590043475](https://steamcommunity.com/sharedfiles/filedetails/?id=3590043475)

Usar como referência para:

- multiplayer;
- Journal Entries;
- diplomacia;
- eventos;
- leis;
- Alemanha;
- sistemas históricos;
- balanceamento;
- decisões;
- mecânicas úteis.

**IMPORTANTE:**\
Não fazer um merge bruto de todo o Tyrone MP.

Inspecionar e identificar quais sistemas são realmente úteis para este projeto. Reimplementar ou adaptar apenas o necessário.

---

### UPDATED Revenge of the Political Map Mode

[https://steamcommunity.com/sharedfiles/filedetails/?id=3275674184](https://steamcommunity.com/sharedfiles/filedetails/?id=3275674184)

Usar como referência para:

- Political Map Mode;
- cores políticas;
- visualização do mapa;
- leitura das fronteiras;
- compatibilidade visual com novos países.

Este sistema deve permanecer essencialmente visual e não deve introduzir processamento pesado de gameplay.

---

### [1.13] Ultra Historical Workforce

[https://steamcommunity.com/sharedfiles/filedetails/?id=3647628156](https://steamcommunity.com/sharedfiles/filedetails/?id=3647628156)

Usar como referência para:

- workforce;
- emprego;
- população economicamente ativa;
- salários;
- industrialização;
- transição de população rural para industrial;
- redução dos gargalos artificiais de workforce.

Não copiar cegamente os valores.

Adaptar o sistema para uma campanha MP 1836–1945.

---

# 2. REGRA FUNDAMENTAL DO PROJETO

A regra mais importante do mod é:

> **Every mechanic must justify its CPU cost.**

Se uma mecânica adiciona pouco gameplay mas aumenta significativamente o processamento, ela deve ser removida, simplificada ou transformada em um sistema event-driven.

Prioridade:

```text
PERFORMANCE
↓
MULTIPLAYER STABILITY
↓
LONG CAMPAIGN
↓
BALANCE
↓
HISTORICAL CONTENT
↓
COSMETICS
```

---

# 3. PRINCÍPIOS DE PERFORMANCE

Evitar ao máximo loops frequentes como:

```txt
every_pop
every_state
every_country
random_country
random_state
```

especialmente dentro de:

```txt
on_daily_pulse
on_weekly_pulse
on_monthly_pulse
```

Sempre que possível, substituir por:

```text
event-driven
↓
yearly
↓
quarterly
↓
monthly
↓
weekly
↓
daily
```

Quanto mais frequente uma operação for executada, maior deve ser a justificativa.

---

# 4. SISTEMA PRINCIPAL — MICROSTATE AI DORMANCY

Criar um sistema de otimização de IA chamado:

# **Microstate AI Dormancy**

O objetivo é reduzir processamento desperdiçado por países pequenos controlados pela IA.

Países pequenos frequentemente não precisam:

- mudar leis constantemente;
- construir grandes exércitos;
- expandir constantemente;
- tomar decisões econômicas complexas;
- recalcular estratégias políticas sem necessidade.

Criar três estados conceituais:

```text
ACTIVE
LIMITED
DORMANT
```

---

# 5. ACTIVE

Aplicar a:

- grandes potências;
- potências regionais;
- países estrategicamente importantes;
- países em situações importantes;
- qualquer país controlado por um player.

Comportamento normal.

---

# 6. LIMITED

Aplicar a países médios ou relativamente pouco importantes.

Reduzir a frequência/intensidade de:

- mudanças de leis;
- expansão militar;
- construções desnecessárias;
- decisões secundárias.

O país continua funcionando normalmente, mas com menos atividade desnecessária.

---

# 7. DORMANT

Aplicar a microestados controlados pela IA que estejam em paz e não tenham importância estratégica imediata.

Enquanto DORMANT:

- reduzir drasticamente desejo de construir exército;
- reduzir expansão militar;
- reduzir mudanças políticas;
- reduzir construção desnecessária;
- manter infraestrutura e economia essencial;
- evitar spam de decisões;
- evitar operações complexas.

---

# 8. REGRA ABSOLUTA DOS PLAYERS

Esta regra é obrigatória:

> **UM PAÍS CONTROLADO POR PLAYER NUNCA PODE ENTRAR EM DORMANCY.**

Mesmo se o país tiver:

- população extremamente pequena;
- GDP extremamente baixo;
- exército pequeno;
- poucos estados;
- pouca importância diplomática.

Exemplo:

```text
Microstate
+
Player = YES
↓
ACTIVE
```

Nunca aplicar ao país do jogador:

- AI sleep;
- AI construction penalties;
- AI military suppression;
- AI law suppression;
- limitações artificiais.

Se um jogador assumir o controle de um país que estava DORMANT:

```text
PLAYER TAKES COUNTRY
↓
WAKE IMMEDIATELY
↓
ACTIVE
```

Se um jogador deixar de controlar o país:

```text
PLAYER LEAVES
↓
CHECK CONDITIONS
↓
DORMANT
```

caso o país continue sendo pequeno, AI-controlled e sem situação importante.

---

# 9. QUANDO UM MICROESTADO NÃO PODE DORMIR

Mesmo sem player, acordar o país se:

```text
war
mobilization
revolution
civil war
diplomatic play
unification opportunity
major journal entry
major economic crisis
major strategic interest
```

Fluxo:

```text
MICROSTATE
   ↓
PEACE + NO PLAYER
   ↓
DORMANT
   ↓
WAR / CRISIS / IMPORTANT EVENT
   ↓
WAKE
   ↓
NORMAL AI
```

Depois de um longo período de estabilidade:

```text
ACTIVE/LIMITED
↓
DORMANT
```

Evitar alternâncias excessivamente frequentes.

---

# 10. CRITÉRIOS DE MICROESTADO

Não usar somente população.

Criar avaliação baseada em vários fatores:

```text
Population
GDP
Military Size
Great Power status
Major Power status
Number of states
Diplomatic importance
Active Journal Entries
War status
Player status
Strategic relevance
```

Os thresholds devem ser configuráveis para balanceamento.

---

# 11. EXÉRCITO DOS MICROESTADOS

Para microestados AI:

### Em paz

- pouca expansão militar;
- baixo desejo de recrutamento;
- evitar exército artificialmente grande;
- evitar marinha desnecessária.

### Em ameaça

- acordar IA;
- preparar defesa.

### Em guerra

- comportamento AI normal.

IMPORTANTE:

Isso nunca deve limitar um player.

---

# 12. LEIS DOS MICROESTADOS

Criar uma forma de **Political Inertia**.

Microestados AI estáveis devem ter menor tendência a mudar leis constantemente.

Não congelar permanentemente.

Permitir mudanças em:

- revolução;
- radicalização;
- guerra;
- mudança de regime;
- crise;
- evento histórico;
- Journal Entry relevante.

---

# 13. CONSTRUÇÃO DOS MICROESTADOS

Microestados AI devem evitar spam de construção.

Prioridade:

1. infraestrutura;
2. edifícios essenciais;
3. setores econômicos relevantes;
4. construção militar apenas quando necessária.

Um país minúsculo não deve gastar processamento tentando administrar uma economia industrial gigantesca que não consegue sustentar.

---

# 14. WORKFORCE

Adaptar o conceito do Ultra Historical Workforce.

Objetivo:

> Evitar que o crescimento econômico do late game seja destruído por falta artificial de workforce.

A população continua importante.

Não criar workforce infinito.

---

# 15. WORKFORCE POR ERA

### 1836–1870

Workforce relativamente restrito.

### 1870–1900

Industrialização e urbanização aumentam workforce disponível.

### 1900–1925

Educação, urbanização e mecanização ajudam a manter o crescimento.

### 1925–1945

Evitar que países industrializados fiquem economicamente paralisados por falta de trabalhadores.

Balancear para que:

```text
population still matters
```

mas:

```text
workforce bottleneck does not kill the late game
```

---

# 16. ALEMANHA

Rework da unificação alemã.

Objetivo:

> Impedir que a Alemanha se torne excessivamente forte cedo demais.

Criar:

- requisitos políticos;
- requisitos diplomáticos/militares;
- possível cap temporal;
- debuff pós-unificação.

---

# 17. UNIFICAÇÃO ALEMÃ

Testar três conceitos:

### Opção A

Hard cap de data.

### Opção B

Unificação precoce possível, mas extremamente custosa.

### Opção C

Combinação:

```text
Before threshold:
restriction

After threshold:
unification possible

After unification:
temporary debuff
```

Preferir a solução que produza melhor MP.

---

# 18. GERMAN UNIFICATION FATIGUE

Após a unificação:

Aplicar temporariamente:

- custo administrativo;
- turbulência política;
- problemas de integração;
- pequeno debuff econômico;
- outras penalidades cuidadosamente balanceadas.

Não destruir a Alemanha.

O objetivo é impedir snowball excessivo.

---

# 19. RESEARCH SLOWDOWN

A pesquisa deve ficar progressivamente mais lenta conforme a campanha avança.

Valores iniciais para teste:

```text
1836–1860 → ×1.00
1860–1880 → ×1.10
1880–1900 → ×1.20
1900–1920 → ×1.30
1920–1945 → ×1.40
```

Estes valores são pontos de partida.

Testar e balancear.

Tecnologias militares podem receber tratamento diferente para garantir evolução militar adequada no período 1930–1945.

---

# 20. RUBBER

Aumentar rubber de maneira balanceada.

Objetivo:

> Rubber deve ser importante e lucrativo, mas não quebrar a economia.

Criar progressão:

```text
1870 → demanda baixa
1890 → demanda média
1910 → demanda alta
1930 → demanda muito alta
1945 → recurso estratégico
```

Possível estrutura:

```text
Natural Rubber
↓
Plantation Rubber
↓
Industrial Rubber
```

Tecnologias tardias podem aumentar eficiência.

A demanda deve crescer com:

- automóveis;
- indústria;
- eletrificação;
- indústria militar;
- produção industrial.

---

# 21. DIREITOS LGBT

Criar sistema político de direitos LGBT sem adicionar simulação individual pesada por Pop.

Usar leis:

```text
Criminalized
Restricted
Tolerated
Protected
Equal
```

A mecânica deve usar principalmente:

- laws;
- political movements;
- parties;
- events;
- Journal Entries;
- modifiers.

Evitar loops sobre todos os Pops.

---

# 22. PARTIDO LGBT

Criar um partido político LGBT/progressista.

Possíveis grupos de apoio:

- Intelligentsia;
- Trade Unions;
- população urbana;
- liberais;
- progressistas.

A emergência do partido pode depender de:

```text
urbanization
literacy
political liberalization
progressive ideology
appropriate laws
```

Evitar verificações constantes.

---

# 23. EVENTOS LGBT

Criar um conjunto pequeno e relevante:

```text
LGBT Rights Movement
Political Debate
Legal Reform
Public Backlash
Rights Expansion
Conservative Reaction
```

Preferir eventos disparados por condições políticas reais.

---

# 24. CAMPOS DE CONCENTRAÇÃO

Criar uma mecânica política/histórica condicionada.

Requisitos principais:

```text
ethnostate
+
one-party state
```

A mecânica deve ser implementada através de:

- Journal Entry;
- laws;
- modifiers;
- eventos limitados.

Não criar loops de Pops para verificar constantemente se a mecânica está disponível.

As leis devem ser suficientes para determinar o acesso à mecânica.

---

# 25. NOVOS GOVERNOS

Adicionar uma quantidade controlada de governos.

## Econômicos

```text
Laissez-Faire Capitalism
Ultra-Capitalism
State Capitalism
Planned Economy
Cooperative Economy
```

## Políticos

```text
Liberal Democracy
Authoritarian Democracy
One-Party State
Technocracy
Military Junta
```

## Alternativos/utópicos

```text
Ultra-Capitalist State
Technocratic State
Corporate State
Workers' Commonwealth
Anarchist Federation
```

Não criar dezenas de governos redundantes.

---

# 26. ULTRA-CAPITALISM

Criar um governo realmente diferente.

Possíveis benefícios:

```text
+ private investment
+ investment pool contribution
+ construction efficiency
+ industrial throughput
+ minting
```

Possíveis custos:

```text
+ inequality
+ radicalization
- welfare efficiency
- worker political power
```

Não transformar em:

```text
+30% GDP
```

sem consequências.

---

# 27. JOURNAL ENTRIES

Aproveitar apenas os JEs úteis das referências.

Categorias:

## Histórico

```text
German Question
Italian Unification
Scramble for Africa
Great War
Great Depression
Second World War
```

## Político

```text
Liberalization
Authoritarian Reaction
LGBT Rights
Political Extremism
```

## Econômico

```text
Industrialization
Rubber Boom
Oil Age
Mass Production
```

Cada JE deve existir porque altera gameplay.

---

# 28. PRIMEIRA GUERRA MUNDIAL

Criar escalada gradual:

```text
1880+
→ rivalidades

1900+
→ alianças e tensões

1910+
→ militarização

1914+
→ possibilidade de grande guerra
```

Não obrigar o conflito em todas as partidas.

Permitir divergência histórica em MP.

---

# 29. SEGUNDA GUERRA MUNDIAL

Criar uma escalada de 1930–1945.

```text
1930+
→ tensão

1933+
→ mudanças políticas

1936+
→ rearmamento

1938+
→ crises diplomáticas

1939+
→ possibilidade de guerra mundial

1941+
→ expansão global

1945
→ final da campanha principal
```

---

# 30. OTIMIZAÇÃO DO LATE GAME

Criar lógica específica para:

```text
1900+
```

e especialmente:

```text
1925+
1935+
```

Sistemas secundários devem reduzir frequência.

Priorizar:

- guerra;
- economia;
- diplomacia;
- tecnologia;
- população;
- principais mecânicas do mod.

Sistemas cosméticos ou secundários podem ser reduzidos ou desligados quando não forem mais relevantes.

---

# 31. POLITICAL MAP MODE

Integrar/adaptar o Political Map Mode da referência.

Objetivos:

- mapa político limpo;
- fronteiras fáceis de visualizar;
- cores consistentes;
- compatibilidade com novos países.

Não adicionar sistemas pesados ao mapa.

---

# 32. PAÍSES

Não adicionar países desnecessariamente.

Novo tag só deve existir se houver motivo:

- jogabilidade;
- história;
- diplomacia;
- unificação;
- mecânica;
- relevância estratégica.

Menos entidades desnecessárias ajudam a manter o late game.

---

# 33. SISTEMAS QUE DEVEM SER EVITADOS

Não adicionar, salvo necessidade extrema:

- clima detalhado;
- espionagem pesada;
- milhares de decisões;
- milhares de eventos aleatórios;
- economia paralela;
- orientação individual de cada Pop;
- IA customizada com verificações constantes;
- excesso de países;
- centenas de modifiers temporários;
- loops globais frequentes;
- sistemas cosméticos que exigem cálculo de gameplay.

---

# 34. MULTIPLAYER

Desenvolver pensando em MP desde o primeiro protótipo.

Testar:

```text
2 players
4 players
8 players
12 players
16 players
```

Testar:

- desync;
- save/load;
- guerras;
- anexações;
- unificações;
- mudanças de governo;
- eventos;
- Journal Entries;
- AI Dormancy;
- wake/sleep;
- economia;
- late game.

---

# 35. PERFORMANCE BENCHMARKS

Realizar campanhas de teste:

```text
1836 → 1870
1836 → 1900
1836 → 1910
1836 → 1930
1836 → 1940
1836 → 1945
```

Medir:

- tempo por tick;
- CPU;
- memória;
- tamanho do save;
- desync;
- número de países;
- comportamento da IA;
- número de Pops;
- guerras;
- economia.

Sempre comparar contra a versão anterior do mod.

---

# 36. TESTES DO MICROSTATE AI

## Teste 1

Microestado AI + paz:

```text
DORMANT
```

## Teste 2

Microestado AI entra em guerra:

```text
WAKE
```

## Teste 3

Microestado AI sofre revolução:

```text
WAKE
```

## Teste 4

Player escolhe microestado:

```text
ACTIVE
```

## Teste 5

Player deixa o microestado:

```text
CHECK CONDITIONS
→ DORMANT
```

## Teste 6

Microestado possui JE importante:

```text
ACTIVE/LIMITED
```

## Teste 7

Crise termina:

```text
ACTIVE/LIMITED
→
DORMANT
```

---

# 37. ROADMAP

## V0.1 — FOUNDATION

- criar mod;
- definir versão;
- estrutura de arquivos;
- metadata;
- integração inicial do mapa;
- documentação;
- regras de código.

## V0.2 — PERFORMANCE

- revisar on\_actions;
- eliminar loops desnecessários;
- reduzir frequência;
- criar Microstate AI Dormancy;
- criar ACTIVE/LIMITED/DORMANT;
- testar MP.

## V0.3 — WORKFORCE

- adaptar conceitos do Ultra Historical Workforce;
- balancear workforce;
- testar 1836–1900.

## V0.4 — GERMANY

- rework da unificação;
- cap/data;
- German Unification Fatigue;
- testes MP.

## V0.5 — ECONOMY

- rubber;
- research slowdown;
- industrialização;
- workforce late game.

## V0.6 — POLITICS

- governos;
- Ultra-Capitalism;
- LGBT rights;
- LGBT party.

## V0.7 — HISTORY

- Journal Entries;
- WW1;
- WW2;
- colonialismo.

## V0.8 — LATE GAME

- otimização 1900–1945;
- AI Dormancy refinada;
- balanceamento.

## V0.9 — STRESS TEST

- multiplayer;
- 1836–1945;
- CPU;
- memória;
- desync;
- guerras;
- economia.

## V1.0 — RELEASE

**Trekker's Victoria: Long Century**

Objetivo:

> Campanha MP completa de 1836 até 1945, com performance sustentável e conteúdo histórico/alternativo suficiente sem transformar o mod em um overhaul excessivamente pesado.

---

# 38. REGRAS DE IMPLEMENTAÇÃO

Ao programar:

1. Não copiar grandes quantidades de código de outros mods sem necessidade.
2. Reimplementar sistemas quando for mais simples e leve.
3. Manter sistemas modulares.
4. Usar nomes claros.
5. Comentar somente onde necessário.
6. Evitar duplicação.
7. Evitar loops globais.
8. Evitar pulses frequentes.
9. Preferir eventos.
10. Manter thresholds e modifiers fáceis de editar.
11. Criar arquivos separados para sistemas diferentes.
12. Não quebrar multiplayer por sistemas locais/aleatórios.
13. Testar cada sistema isoladamente.
14. Fazer benchmark antes e depois de grandes alterações.
15. Não adicionar conteúdo apenas para aumentar o tamanho do mod.

---

# 39. PRINCÍPIO DE DESIGN FINAL

O mod deve seguir três regras:

> **Grandes países simulam.**

> **Pequenos países sobrevivem.**

> **Jogadores nunca dormem.**

E uma quarta:

> **Se não melhora significativamente o gameplay, não vale o custo de CPU.**

O resultado esperado é um Victoria 3 que:

- começa em 1836;
- funciona bem em multiplayer;
- possui conteúdo histórico e alternativo;
- mantém economia funcional;
- possui sistemas políticos adicionais;
- possui workforce equilibrado;
- possui rubber relevante;
- possui Alemanha balanceada;
- possui pesquisa progressiva;
- possui AI otimizada;
- possui microestados dormentes;
- permite jogadores escolherem qualquer país sem serem prejudicados;
- chega a 1945;
- e evita que o late game se transforme em uma apresentação de slides.\
  """
