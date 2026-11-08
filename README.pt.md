# awk Cheatsheet · Folha de referência de comandos awk

> 🌍 [中文](README.md) · [Español](README.es.md)

Uma **folha de referência de uma única página** com 30 usos reais do `awk`: a forma `padrão { ação }` da qual nasce todo programa awk; campos, registros e os dois contadores de linha; seleção de linhas com regex, intervalos e comparações; as variáveis e funções de texto que fazem o trabalho pesado; e as receitas de uma linha que as pessoas realmente guardam no histórico (somar, tirar média, deduplicar, agrupar, juntar dois arquivos). Cada entrada responde apenas três perguntas: **o que faz, um comando pronto para copiar e colar, e etiquetas de uso**. Sem sintaxe inventada: as 30 entradas foram **executadas de verdade**, uma a uma, contra arquivos de teste no mawk 1.3.4, e a semântica foi conferida no Guia do Usuário do GNU Awk. As diferenças entre gawk e mawk/BusyBox estão assinaladas no fim da folha: algumas falham em silêncio.

## Para quem é

Engenheiros de operações (SRE/DevOps), desenvolvedores backend e qualquer pessoa que tenha o log na frente mas não consiga tirar a soma por grupo, ou que para cruzar dois arquivos por uma chave acabe escrevendo um script. Se o `grep` só filtra linhas e o `sed` só substitui, e o que você precisa é **entender colunas, fazer aritmética e manter totais entre linhas**, esta folha é para você.

## Como usar

1. Vá direto ao **[CHEATSHEET.md](CHEATSHEET.md)**: o corpo da referência; os comandos em inglês qualquer desenvolvedor entende.
2. Identifique primeiro o seu awk: `awk --version` diz gawk ou mawk na primeira linha; o macOS traz o awk do BSD. As 30 entradas estão verificadas no mawk 1.3.4 e as notas de implementação no final são leitura obrigatória.
3. Siga a ordem da folha —conceitos básicos → padrões → variáveis e funções → receitas— e teste cada comando à mão sobre arquivos de prática. Em uma hora você domina.

## Experimente em 30 segundos

```bash
awk --version | head -1                # confirma a implementação: gawk / mawk / BSD
printf 'alice math 88\nbob math 72\n' > /tmp/scores.txt
awk '{s += $3} END {print s}' /tmp/scores.txt   # 160: a soma de uma coluna, o gesto clássico do awk
```

## A combinação de ouro, pronta para usar

```bash
awk 'NR == FNR {tier[$1] = $2; next} {print $1, $2, tier[$1]}' codes.txt events.txt
```

A junção de dois arquivos: o primeiro é carregado na memória como tabela de consulta (na fase `NR == FNR` ele só é guardado), o segundo é percorrido linha a linha acrescentando o valor encontrado. Sem isso você precisaria de um script ou de um banco de dados; com isso, uma linha.

## O que inclui

| Categoria | Conteúdo |
|---|---|
| 🧱 Conceitos básicos | print e $0 a linha inteira / campos $1, $NF e NF / os contadores NR e FNR / BEGIN e END / comportamento padrão de padrão e ação / variáveis próprias e -v (6) |
| 🎯 Padrões | /regex/ sobre a linha inteira / intervalo /início/,/fim/ / a armadilha de tipos nas comparações / ~ e !~ por campo / &&, ||, ! / FNR>1 para pular o cabeçalho de cada arquivo (6) |
| 🧰 Variáveis e funções | -F e FS / reconstrução de $0 e OFS ao atribuir um campo / length / substr / split / sub e gsub / printf e sprintf / match com RSTART e RLENGTH / toupper e index (9) |
| 🍳 Receitas | soma de coluna / média com proteção contra arquivo vazio / deduplicar mantendo a ordem com !seen[$0]++ / soma por grupos / frequência de palavras / junção NR==FNR / reordenar colunas CSV / o aviso sobre CSV / a linha mais longa (9) |

A documentação oficial vinculada é apenas https://www.gnu.org/software/gawk/manual/; todos os comandos foram verificados contra o Guia do Usuário do GNU Awk e executados com sucesso no mawk 1.3.4; os exemplos operam somente sobre arquivos de teste criados na hora, sem dados reais.

## Por que estas 30

- **Os contadores, claros desde o início**: NR e FNR diferem por uma letra, mas governam duas tarefas enormes —pular cabeçalhos e juntar arquivos—; pular cabeçalhos com NR deixa passar o do segundo arquivo como se fosse dado, em silêncio. A folha marca com ⚠️ e dá a forma correta.
- **A armadilha de tipos nas comparações tinha de estar**: `"010" == 10` é verdadeiro e `"010" == "10"` é falso: as aspas da constante escolhem comparação numérica ou de texto. É o ponto menos intuitivo do awk e precisa ser dito antes que ele morda.
- **As diferenças de implementação, à vista**: `gensub`, `BEGINFILE` e `FPAT` são exclusivos do gawk; o mawk protesta com `gensub`, mas **ignora em silêncio** os outros dois: o script roda e o resultado sai errado sem aviso. No fim da folha estão enumerados, com a conclusão medida nesta máquina.
- **Receitas que sobrevivem no histórico**: somar, deduplicar, agrupar e juntar são o grosso do uso diário do awk; cada uma vem na forma em que basta trocar o número do campo.

## Pull Requests são bem-vindos

Um comando incorreto, uma opção obsoleta, uma tradução a melhorar? Pull Requests são bem-vindos. Mantenha o formato das entradas (uma linha de explicação / bloco bash / etiquetas) e **apenas comandos reais e verificados; na dúvida, deixe de fora**.

## 📚 Mais folhas de referência

Outras folhas do mesmo autor, também em chinês / espanhol / português:

- [Folha de SSH](https://github.com/Ezra-Zhao/ssh-cheatsheet) — 28 comandos de conexão, chaves, túneis e endurecimento
- [Folha de FFmpeg](https://github.com/Ezra-Zhao/ffmpeg-cheatsheet) — 28 comandos de áudio e vídeo
- [Folha de Git](https://github.com/Ezra-Zhao/git-cheatsheet) — 30 comandos do dia a dia e de resgate
- [Folha de Docker](https://github.com/Ezra-Zhao/docker-cheatsheet) — 28 comandos de contêineres
- [Folha de curl](https://github.com/Ezra-Zhao/curl-cheatsheet) — 29 comandos para depurar APIs
- [Folha de expressões regulares](https://github.com/Ezra-Zhao/regex-cheatsheet) — 30 padrões práticos
- [Folha de jq](https://github.com/Ezra-Zhao/jq-cheatsheet) — 30 filtros para JSON
- [Folha de superpoderes de terminal](https://github.com/Ezra-Zhao/terminal-superpowers-cheatsheet) — 14 ferramentas de produtividade
- [Folha de find + xargs](https://github.com/Ezra-Zhao/find-xargs-cheatsheet) — 30 comandos para buscar arquivos e agir em lote
- [Folha de kubectl](https://github.com/Ezra-Zhao/kubectl-cheatsheet) — comandos habituais do Kubernetes
- [Folha de SQL](https://github.com/Ezra-Zhao/sql-cheatsheet) — consultas e funções habituais
- [Folha de Vim](https://github.com/Ezra-Zhao/vim-cheatsheet) — 30 comandos de edição, busca e macros
- [Folha de tmux](https://github.com/Ezra-Zhao/tmux-cheatsheet) — 30 comandos de sessões, janelas e painéis
- [Folha de systemd](https://github.com/Ezra-Zhao/systemd-cheatsheet) — 30 comandos de serviços e registros

## Licença

MIT © 2026 Guangyi Zhao. Veja [LICENSE](LICENSE).
