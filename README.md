# Forja — Algoritmos + POO em .NET

Trilha unificada: **cada semana junta um tema de algoritmos/estruturas de dados com um conceito de POO**, num projeto que obriga você a fazer as duas coisas:

- **Implementar** — escrever a estrutura/algoritmo do zero, bem modelado (classe genérica, interface, encapsulamento, testes).
- **Utilizar** — consumir o que você construiu dentro de uma mini-aplicação orientada a objetos com regra de negócio de verdade.

Ao longo das semanas, tudo que você implementa vai para uma biblioteca própria, a **Forja.Core**, que as semanas seguintes e as aplicações finais são obrigadas a usar.

---

## Regras do jogo

1. **Na mão primeiro.** Nada de `List<T>`, `Dictionary`, `PriorityQueue`, `SortedDictionary` ou `Array.Sort` para o tema da semana: você usa a sua versão. Frameworks (EF Core, Moq, libs de DI) só aparecem depois da versão caseira.
2. **Sua biblioteca é obrigatória.** Depois que uma estrutura entra na `Forja.Core`, as semanas seguintes usam ela, não a do .NET. Se ela tiver bug ou API ruim, você sente na pele e corrige. Esse é o ponto.
3. **Toda estrutura tem teste e benchmark.** Testes em xUnit desde a semana 1; benchmark com BenchmarkDotNet comparando sua versão com a equivalente do .NET, e o resultado vai no README junto com a complexidade esperada.
4. **Complexidade documentada.** Todo método público da `Forja.Core` tem um comentário XML com a complexidade de tempo e espaço.
5. **README por semana:** o que implementei, onde usei, Big-O medido vs esperado, o que me confundiu.

## Ritmo semanal (~1h/dia, 5 dias)

| Dia | Foco |
|---|---|
| Seg | Teoria do tema de algoritmos + 1–2 problemas |
| Ter | 2–3 problemas do tema (pasta `problemas/`) |
| Qua | **Implementar** na `Forja.Core` + testes |
| Qui | **Utilizar** no app da semana (conceito de POO) |
| Sex | Benchmark vs .NET, README, revisão de 1 problema antigo |

Semanas densas podem virar duas sem culpa — o cronograma tem folga no fim do ano.

## Estrutura do repositório

```
forja/
├── Forja.sln
├── Directory.Build.props      # Nullable, TreatWarningsAsErrors
├── src/Forja.Core/            # sua biblioteca: Colecoes/, Algoritmos/, Grafos/, Dominio/
├── tests/Forja.Core.Tests/
├── benchmarks/Forja.Benchmarks/
├── problemas/                 # soluções dos exercícios, um arquivo por problema
├── semanas/
│   ├── S01-complexidade/
│   ├── S02-hashing/
│   └── ...
└── apps/                      # fase 2
```

---

# Fase 1 — Fundação (16 semanas)

### S01 · Notação assintótica × Classes, encapsulamento e delegates
- **Algoritmos:** Big-O, Ω, Θ; melhor/pior/médio caso; complexidade de espaço.
- **POO:** classe, construtor, propriedades, invariantes, `Func<T>`/`Action`.
- **Implementar:** montar o repositório (solution, Core, testes, benchmarks). Classe `Experimento` que recebe uma `Func<int, long>` e uma lista de tamanhos de entrada, mede tempo e operações, e imprime a curva de crescimento.
- **Utilizar:** rodar o `Experimento` com funções O(1), O(log n), O(n), O(n log n), O(n²) e confirmar as curvas; depois repetir com BenchmarkDotNet.
- **Pronto quando:** `dotnet build` e `dotnet test` limpos, e você reconhece cada curva só pelo gráfico/tabela.

### S02 · Arrays e hashing × Value Objects, `Equals` e `GetHashCode`
- **Algoritmos:** arrays dinâmicos, hashing, colisões (encadeamento), fator de carga, rehash. Problemas: Two Sum, Contains Duplicate, Valid Anagram, Group Anagrams.
- **POO:** imutabilidade, igualdade por valor, `record` vs `class`, sobrecarga de operadores.
- **Implementar:** `ListaDinamica<T>` (array que cresce) e `TabelaHash<TChave, TValor>` com encadeamento e rehash.
- **Utilizar:** criar os VOs `Cpf` (com dígito verificador) e `Dinheiro`, usá-los como **chave** na sua tabela hash num índice de clientes. Experimento: quebre o `GetHashCode` do `Cpf` de propósito e meça o que acontece com o desempenho.
- **Pronto quando:** você consegue explicar, com o benchmark, por que um `GetHashCode` ruim transforma O(1) em O(n).

### S03 · Two pointers e sliding window × Encapsulamento de coleções e invariantes
- **Algoritmos:** dois ponteiros em array ordenado, janela fixa e variável. Problemas: Valid Palindrome, 3Sum, Container With Most Water, Longest Substring Without Repeating Characters.
- **POO:** invariantes, `IReadOnlyList<T>`, coleção interna protegida.
- **Implementar:** extensões genéricas `JanelaDeslizante` na `Forja.Core` (ex.: maior soma em janela, janela que satisfaz um predicado).
- **Utilizar:** `ContaBancaria` com histórico encapsulado (usando sua `ListaDinamica`). Um `DetectorDeFraude` usa janela deslizante (mais de N saques em X minutos) e um `ConciliadorDeExtratos` usa dois ponteiros para casar dois extratos ordenados.
- **Pronto quando:** é impossível alterar o histórico de fora da conta, e o detector roda em O(n).

### S04 · Pilhas, filas e listas ligadas × Generics, `IEnumerable<T>` e Command
- **Algoritmos:** lista simples e dupla, pilha, fila com array circular. Problemas: Valid Parentheses, Min Stack, Reverse Linked List, Merge Two Sorted Lists, Evaluate Reverse Polish Notation.
- **POO:** generics com constraints, `IEnumerable<T>` com `yield return`, indexadores, padrão Command.
- **Implementar:** `ListaLigada<T>`, `Pilha<T>`, `Fila<T>` (circular), todas funcionando com `foreach` e LINQ.
- **Utilizar:** editor de texto no console com `Inserir`, `Apagar`, `Desfazer`, `Refazer`, onde cada ação é um `IComando` guardado nas **suas** pilhas. Bônus: calculadora de expressões (Shunting-yard) usando sua pilha e fila.
- **Pronto quando:** `minhaLista.Where(x => x > 3).ToList()` funciona e o undo/redo nunca corrompe o texto.

### S05 · Busca binária × Interfaces, `IComparable<T>` e `IComparer<T>`
- **Algoritmos:** busca binária clássica, lower/upper bound, busca binária na resposta. Problemas: Binary Search, Search in Rotated Sorted Array, Find Minimum in Rotated Sorted Array, Koko Eating Bananas.
- **POO:** interfaces, contratos do .NET, comparadores intercambiáveis.
- **Implementar:** `BuscaBinaria.Encontrar<T>(IReadOnlyList<T>, T, IComparer<T>)`, `LimiteInferior`, `LimiteSuperior` e `BuscarNaResposta(min, max, Func<long,bool>)`.
- **Utilizar:** ranking de jogadores (`Jogador : IComparable<Jogador>`) com comparadores alternativos (por nome, por data); e um planejador de entregas que usa busca na resposta para achar a menor capacidade de caminhão que entrega tudo em D dias.
- **Pronto quando:** a mesma busca funciona com qualquer critério só trocando o `IComparer<T>`.

### S06 · Ordenação e divisão e conquista × Herança, classe abstrata e Template Method → Strategy
- **Algoritmos:** bubble, insertion, selection, merge sort, quick sort, estabilidade, D&C. Problemas: Sort an Array, Merge Intervals, Kth Largest Element (via quickselect), Sort Colors.
- **POO:** classe abstrata, `abstract`/`virtual`/`override`, Template Method, depois refatoração para Strategy.
- **Implementar:** **versão 1:** `AlgoritmoOrdenacao<T>` abstrata com o método-template `Ordenar()` que conta comparações e trocas, e subclasses para cada algoritmo. **Versão 2:** refatorar para `IEstrategiaOrdenacao<T>` com um `OrdenacaoInstrumentada<T>` por composição. Comparar as duas no README.
- **Utilizar:** folha de pagamento (`Funcionario` abstrata → `Clt`, `Pj`, `Estagiario`) com relatório ordenável por salário, nome ou admissão usando as **suas** ordenações; comparar estabilidade do merge vs quick na prática.
- **Pronto quando:** você mostra com um exemplo por que merge sort é estável e quick sort não, e tem opinião sobre herança vs composição.

### S07 · Recursão e backtracking × Composite e polimorfismo
- **Algoritmos:** recursão, pilha de chamadas, backtracking com poda. Problemas: Subsets, Permutations, Combination Sum, N-Queens, Word Search.
- **POO:** padrão Composite, polimorfismo recursivo, interface genérica de problema.
- **Implementar:** `IProblemaBacktracking<TEstado>` (candidatos, é válido, é solução, aplicar, desfazer) e um `ResolvedorBacktracking<TEstado>` genérico.
- **Utilizar:** resolver N-Rainhas e Sudoku implementando só a interface. Em paralelo, um sistema de arquivos com Composite (`Pasta` e `Arquivo` como `INoSistema`) calculando tamanho e buscando arquivos recursivamente.
- **Pronto quando:** um problema de backtracking novo é só uma classe nova; o resolvedor não muda.

### S08 · Árvores binárias e BST × Iterators e polimorfismo em árvore de expressão
- **Algoritmos:** percursos (pré, em, pós-ordem, por nível), BST (inserir, buscar, remover), altura, balanceamento (noção). Problemas: Maximum Depth, Invert Binary Tree, Validate BST, Lowest Common Ancestor, Level Order Traversal.
- **POO:** `IEnumerable<T>` com percurso in-order via `yield`, classes abstratas para nós, polimorfismo.
- **Implementar:** `ArvoreBusca<TChave, TValor>` (dicionário ordenado) com `Min`, `Max`, `Remover`, `Intervalo(de, ate)` e enumeração em ordem.
- **Utilizar:** avaliador de expressões matemáticas: `Expressao` abstrata → `Numero`, `Soma`, `Multiplicacao`, `Variavel`, cada nó sabendo se avaliar e se imprimir. E um catálogo de livros indexado por ISBN na sua BST.
- **Pronto quando:** `foreach` na sua árvore sai ordenado e o avaliador não tem nenhum `switch` de tipo de nó.

### S09 · Heaps e filas de prioridade × Eventos, delegates e Observer
- **Algoritmos:** heap binário em array, sift up/down, heapify, heap sort, top-K. Problemas: Kth Largest in a Stream, Last Stone Weight, K Closest Points, Merge K Sorted Lists, Find Median from Data Stream.
- **POO:** eventos, `EventHandler<TEventArgs>`, Observer nativo do C#, desinscrição e memory leak.
- **Implementar:** `HeapBinario<T>` e `FilaDePrioridade<TElemento, TPrioridade>`.
- **Utilizar:** agendador de tarefas (simulação de escalonador) que usa sua fila de prioridade e dispara eventos `TarefaIniciada`, `TarefaAtrasada`, `FilaCheia`; assinantes independentes (log, alerta, métricas).
- **Pronto quando:** o agendador não sabe quem está ouvindo e o heap passa em testes com 100 mil operações aleatórias contra o `PriorityQueue` do .NET.

### S10 · Grafos: representação, BFS e DFS × Abstração, LSP e ISP
- **Algoritmos:** lista vs matriz de adjacência, BFS, DFS, componentes conexas, detecção de ciclo. Problemas: Number of Islands, Clone Graph, Flood Fill, Rotting Oranges, Course Schedule.
- **POO:** interface bem segregada (`IGrafo<T>`, `IGrafoPonderado<T>`), Liskov: duas implementações devem ser 100% intercambiáveis.
- **Implementar:** `IGrafo<T>` com `GrafoListaAdjacencia<T>` e `GrafoMatrizAdjacencia<T>`; `Bfs` e `Dfs` que funcionam com qualquer `IGrafo<T>`. Usa sua `Fila<T>` e `Pilha<T>`.
- **Utilizar:** dungeon crawler no console: mapa em grade vira grafo, BFS acha o menor caminho até a saída, DFS descobre salas alcançáveis, monstros como `IEntidade` com comportamentos diferentes.
- **Pronto quando:** a mesma bateria de testes passa nas duas implementações de grafo (prova de LSP).

### S11 · Grafos: ordenação topológica e caminho mínimo × DIP e container de DI caseiro
- **Algoritmos:** ordenação topológica (Kahn e DFS), detecção de ciclo em grafo dirigido, Dijkstra (com seu heap), noção de Bellman-Ford. Problemas: Course Schedule II, Network Delay Time, Cheapest Flights Within K Stops.
- **POO:** Dependency Inversion, injeção por construtor, ciclo de vida, reflection básico.
- **Implementar:** `OrdenacaoTopologica` e `Dijkstra` na `Forja.Core`.
- **Utilizar:** **container de DI escrito na mão**: `Registrar<TInterface, TImpl>()` e `Resolver<T>()`. Ele monta o grafo de dependências dos construtores, usa **sua ordenação topológica** para decidir a ordem de criação e **detecção de ciclo** para dar erro em dependência circular. Bônus: `ICalculadoraFrete` com implementação baseada em Dijkstra sobre um mapa de cidades, injetada pelo seu container.
- **Pronto quando:** seu container resolve uma árvore de 3 níveis e acusa dependência circular com mensagem clara.

### S12 · Greedy × Strategy, Specification e testes com fakes
- **Algoritmos:** escolha gulosa, prova informal (troca), quando falha. Problemas: Jump Game, Gas Station, Non-overlapping Intervals, Meeting Rooms II, Partition Labels.
- **POO:** Strategy, Specification (`And`, `Or`, `Not`), test doubles escritos à mão (stub, fake, spy).
- **Implementar:** `EscalonadorDeIntervalos` guloso e `TrocoGuloso` na `Forja.Core`.
- **Utilizar:** agenda de salas de reunião: reservas alocadas por algoritmo guloso, regras de negócio como `ISpecification<Reserva>` combináveis (horário comercial, capacidade, sem conflito). Testes com `IRelogio` stub e repositório fake feitos na mão.
- **Experimento:** achar um conjunto de moedas em que o `TrocoGuloso` erra. Guarde, ele volta na S14.
- **Pronto quando:** regra nova de reserva é uma classe nova, e você tem um contraexemplo documentado do guloso.

### S13 · DP 1D e memoization × Decorator e Proxy
- **Algoritmos:** subproblemas sobrepostos, memoization (top-down) vs tabulação (bottom-up). Problemas: Climbing Stairs, House Robber, Min Cost Climbing Stairs, Decode Ways.
- **POO:** Decorator, Proxy, composição de comportamento sem alterar a classe original.
- **Implementar:** `Memoizador` genérico que transforma qualquer `Func<TEntrada, TSaida>` em versão memoizada (usando **sua** `TabelaHash`).
- **Utilizar:** repositório lento simulado (`IRepositorioProdutos` com atraso artificial) envolvido em decorators empilháveis: `CacheDecorator` (mesma ideia da memoization), `LogDecorator`, `RetryDecorator`. Ligar e desligar cada um só mudando a composição.
- **Pronto quando:** você explica que memoization é um cache, e cache é um decorator, com código seu provando.

### S14 · DP 1D avançado × State e Result pattern
- **Algoritmos:** DP com escolhas, reconstrução da solução. Problemas: Coin Change, Longest Increasing Subsequence, Word Break, Partition Equal Subset Sum.
- **POO:** padrão State, exceções de domínio vs `Result<T>` escrito na mão.
- **Implementar:** `TrocoOtimo` (DP) na `Forja.Core`, e rodar o contraexemplo da S12 para mostrar o guloso errando e a DP acertando.
- **Utilizar:** caixa eletrônico com máquina de estados (`Ocioso → CartaoInserido → Autenticado → Sacando → Ocioso`, com `Bloqueado` após 3 senhas erradas). O saque usa `TrocoOtimo` com as cédulas disponíveis no cofre e retorna `Result<Cedulas>` em vez de lançar exceção quando não há combinação.
- **Pronto quando:** transição inválida de estado é impossível e o caixa nunca entrega valor errado.

### S15 · DP 2D × Builder e Factory
- **Algoritmos:** DP em grade e em duas sequências. Problemas: Unique Paths, Longest Common Subsequence, Edit Distance, 0/1 Knapsack, Longest Palindromic Substring.
- **POO:** Builder fluente, Factory Method.
- **Implementar:** `DistanciaEdicao` e `MaiorSubsequenciaComum` (com reconstrução) e `Mochila01`.
- **Utilizar:** ferramenta de **diff** que compara duas versões de texto do editor da S04 e mostra linhas adicionadas/removidas via LCS, configurada por um `DiffBuilder` (ignorar espaços, ignorar maiúsculas, contexto). E um otimizador de carga (mochila) criado por uma factory que escolhe entre DP e guloso conforme o tamanho da entrada.
- **Pronto quando:** o diff funciona em dois arquivos reais seus e você sabe justificar a escolha da factory.

### S16 · Revisão e consolidação da Forja.Core
- Revisar a API pública de toda a biblioteca: nomes, consistência, interfaces.
- Garantir testes para todas as estruturas e uma tabela única de benchmarks (sua versão vs .NET) no README principal.
- Refazer sem olhar 5 problemas que mais deram trabalho.
- Empacotar a `Forja.Core` como pacote NuGet local para a Fase 2 consumir.
- **Pronto quando:** você consegue apresentar a biblioteca inteira em 10 minutos, com a complexidade de cada operação de cabeça.

---

# Fase 2 — Aplicações completas (usando a Forja.Core)

Aqui o foco vira arquitetura e modelagem de domínio. Algoritmos continuam em revisão contínua (2–3 problemas por semana).

### A1 · Sistema de Biblioteca em camadas (2 semanas)
- Domínio rico: `Livro`, `Exemplar`, `Membro`, `Emprestimo`, `Reserva`, `Multa`; entidades vs value objects, agregados, domain events.
- Camadas `Domain`, `Application`, `Infrastructure`, `ConsoleUI`.
- **Usa a Forja:** índice de acervo na sua `ArvoreBusca` (busca por intervalo de ISBN/título), fila de reservas na sua `Fila<T>`, repositório em memória sobre sua `TabelaHash`.
- Persistência trocável: memória → JSON escrito na mão → EF Core + SQLite, sem mudar o domínio.
- **Pronto quando:** os mesmos testes de aplicação passam nas três persistências.

### A2 · API de E-commerce (2–3 semanas)
- ASP.NET Core Web API + EF Core, Clean Architecture leve, JWT, middleware mapeando erros de domínio para HTTP.
- **Usa a Forja:** frete por Dijkstra (S11), estados do pedido (padrão da S14), cache por decorator (S13), busca de produtos com sua BST, recomendação "top-K mais vendidos" com seu heap.
- Testes de unidade + integração com `WebApplicationFactory`.
- **Pronto quando:** carrinho → checkout → pagamento → envio funciona ponta a ponta via HTTP.

### A3 · Help Desk de chamados — capstone (3 semanas)
- Domínio: `Chamado`, `Solicitante`, `Atendente`, `Fila`, `SLA`, `Comentario`; permissões polimórficas por perfil.
- **Usa a Forja:** triagem com sua `FilaDePrioridade` (prioridade + tempo de espera), cadeia de escalonamento como grafo dirigido com ordenação topológica, busca de chamados similares com `DistanciaEdicao`, detecção de picos com janela deslizante, eventos de domínio para notificações.
- `BackgroundService` verificando SLA estourado; banco à escolha (SQL Server, PostgreSQL ou Oracle); front React/Next opcional.
- **Pronto quando:** você explica cada decisão de algoritmo e de design numa conversa de entrevista.

---

## Mapa: onde cada conceito aparece

| Conceito de POO | Semana |
|---|---|
| Classes, encapsulamento, delegates | S01, S03 |
| Value objects, igualdade | S02 |
| Generics, `IEnumerable<T>`, iterators | S04, S08 |
| Interfaces, comparadores | S05, S10 |
| Herança, classe abstrata, Template Method | S06 |
| Composição sobre herança | S06, S07 |
| Polimorfismo, Composite | S07, S08 |
| Eventos, Observer | S09 |
| SOLID (LSP, ISP, DIP) | S10, S11 |
| DI na mão | S11 |
| Strategy, Specification, test doubles | S06, S12 |
| Decorator, Proxy | S13 |
| State, Result pattern | S14 |
| Builder, Factory | S15 |
| DDD tático, Repository, camadas | A1, A2, A3 |

## Checklist por semana

- [ ] 3–5 problemas do tema resolvidos em `problemas/`
- [ ] Estrutura/algoritmo na `Forja.Core`, com testes e complexidade documentada
- [ ] App da semana usando a sua implementação (não a do .NET)
- [ ] Benchmark sua versão vs .NET
- [ ] README da semana
- [ ] 1 problema antigo refeito sem olhar
- [ ] Task marcada como Done no Life OS
