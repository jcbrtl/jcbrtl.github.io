# A linguagem Awk de programação: introdução e referência

Autor da síntese: J. Cesar Bertelli <jcbrtl-@-usp-.-br>

Este documento é mantido em https://jcbrtl.github.io/docs/awk

Versão do documento: 6.1.2020

## Dedicatória
Aos novos cientistas dos dados brasileiros, que percebam esta novidade de 1977.

## Conteúdo
   * [Awk em 10 minutos](#awk-em-10-minutos)
      * [Estrutura dum programa em Awk](#estrutura-dum-programa-em-awk)
      * [Pares padrão-ação](#pares-padrão-ação)
      * [A variável NF](#a-variável-nf)
      * [A variável NR](#a-variável-nr)
      * [Inserindo texto na saída](#inserindo-texto-na-saída)
      * [Padrões uteis (para seleção)](#padrões-uteis-para-seleção)
         * [Comparação](#comparação)
         * [Computação](#computação)
         * [Conteúdo textual](#conteúdo-textual)
      * [Combinando padrões com &amp;&amp; e; || ou; ! não](#combinando-padrões-com--e--ou--não)
      * [Validação de dados](#validação-de-dados)
      * [Padrões especiais](#padrões-especiais)
      * [Computando com Awk](#computando-com-awk)
         * [Contando](#contando)
         * [Concatenando strings](#concatenando-strings)
         * [Funções embutidas](#funções-embutidas)
         * [Controle na execução](#controle-na-execução)
            * [if/else](#ifelse)
            * [while](#while)
            * [for](#for)
         * [Arrays](#arrays)
   * [Awk numa única linha (exemplos)](#awk-numa-única-linha-exemplos)
   * [Para saber <em>tudo</em> sobre Awk](#para-saber-tudo-sobre-awk)
      * [Quebrando uma instrução em varias linhas](#quebrando-uma-instrução-em-varias-linhas)
      * [Padrão por limites](#padrão-por-limites)
      * [A variável FS](#a-variável-fs)
      * [Operadores](#operadores)
         * [Exemplos](#exemplos)
            * [Teste de strings (se numa string está contida certa substring)](#teste-de-strings-se-numa-string-está-contida-certa-substring)
      * [Metacaracteres (/regexpr/)](#metacaracteres-regexpr)
         * [Exemplos](#exemplos-1)
      * [Padrões compostos](#padrões-compostos)
      * [A variável FNR](#a-variável-fnr)
      * [A variável FILENAME](#a-variável-filename)
      * [Ações](#ações)
      * [A variável OFS](#a-variável-ofs)
      * [Outras variáveis embutidas](#outras-variáveis-embutidas)
      * [Cria campo adicional nas LsE](#cria-campo-adicional-nas-lse)
      * [Operadores aritméticos](#operadores-aritméticos)
      * [Outros operadores](#outros-operadores)
         * [Atributivos](#atributivos)
         * [Unários](#unários)
         * [Incrementador e decrementador (prefixos e sufixos)](#incrementador-e-decrementador-prefixos-e-sufixos)
      * [Valor de verdade duma comparação](#valor-de-verdade-duma-comparação)
      * [Expressão condicional](#expressão-condicional)
      * [Atribuições em testes](#atribuições-em-testes)
      * [Funções aritméticas embutidas. Se x, y são expressões:](#funções-aritméticas-embutidas-se-x-y-são-expressões)
      * [Concatenação de strings](#concatenação-de-strings)
      * [Strings como regexpr](#strings-como-regexpr)
      * [Funções embutidas para strings.](#funções-embutidas-para-strings)
      * [Arrays](#arrays-1)
         * [Verificando a existencia dum subscript](#verificando-a-existencia-dum-subscript)
         * [Deletando um elemento](#deletando-um-elemento)
         * [Simulando arrays multi-D](#simulando-arrays-multi-d)
            * [Verificando](#verificando)
            * [Iterando](#iterando)
      * [Funções definidas pelo usuario](#funções-definidas-pelo-usuario)
      * [Saída](#saída)
         * [A vírgula no print](#a-vírgula-no-print)
         * [Formatação do printf, \x[-][w][.p]c:](#formatação-do-printf--wpc)
         * [Exemplo de filtragem e separação, com &gt;](#exemplo-de-filtragem-e-separação-com-)
      * [Pipes](#pipes)
         * [Fechando arquivos e pipes](#fechando-arquivos-e-pipes)
      * [Entrada](#entrada)
      * [Separador de campos variável (por LE), usando regexpr](#separador-de-campos-variável-por-le-usando-regexpr)
      * [Processando registros com mais de uma linha](#processando-registros-com-mais-de-uma-linha)
      * [A função getline](#a-função-getline)
         * [O valor duma expr getline](#o-valor-duma-expr-getline)
            * [Exemplo: substituindo '#include "file"' pelo conteúdo de file](#exemplo-substituindo-include-file-pelo-conteúdo-de-file)
            * [Exemplo: contando usuários logados](#exemplo-contando-usuários-logados)
            * [Exemplo: pondo a data numa variável](#exemplo-pondo-a-data-numa-variável)
      * [Atribuições de variáveis pela linha de comando](#atribuições-de-variáveis-pela-linha-de-comando)
      * [Argumentos da linha de comando](#argumentos-da-linha-de-comando)
         * [Exemplo: imprimindo argumentos](#exemplo-imprimindo-argumentos)
         * [Exemplo: imitando seq](#exemplo-imitando-seq)
      * [Interagindo com outros programas](#interagindo-com-outros-programas)
         * [A função system](#a-função-system)
            * [Exemplo: inclusao de arquivo](#exemplo-inclusao-de-arquivo)
      * [Faca do seu programa em Awk um cmd shell](#faca-do-seu-programa-em-awk-um-cmd-shell)
      * [(Opcoes da linha de comando)](#opcoes-da-linha-de-comando)
   * [Outros exemplos](#outros-exemplos)
      * [Padrao para impressao (tratamento horizontal)](#padrao-para-impressao-tratamento-horizontal)
      * [Usando regexpr (e array binario) para tratar entrada mista](#usando-regexpr-e-array-binario-para-tratar-entrada-mista)
      * [Mudanca de escala](#mudanca-de-escala)
      * [Histograma](#histograma)
      * [Gerando inteiros](#gerando-inteiros)



## Licença

<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Licença Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />Este obra está licenciado com uma Licença <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Atribuição-NãoComercial 4.0 Internacional</a>.

https://creativecommons.org/licenses/by-nc/4.0/deed.pt_BR

## Referência
A. V. Aho, B. W. Kernighan, P. J. Weinberger, **The AWK Programming Language**, Addison-Wesley, 1988.  ISBN 0-201-07981-X


# Awk em 10 minutos

Um programa em Awk é uma sequência de padrões (que dizem pelo que procurar) e ações (que dizem o que fazer quando o encontrar).

Awk vasculha os arquivos de entrada e põe automaticamente cada linha de entrada num bloco individual, no qual os campos se separam, e são identificáveis pela variável $n, 1 <= n <= NF. A linha toda é identificável por $0. O comportamento padrão do Awk é entender um campo como uma sequência de caracteres na qual não existem brancos ou tabs. [ALT]

```
$ cat emp.data 
Xeth	4.00	0
Kathy	4.00	10
Susie	4.25	18
Tanna	5.75	19
Yka		4.00	0
```


## Estrutura dum programa em Awk
> $ awk 'programa' arquivo

```
$ awk 'padrão { ação }
[padrão { ação }
...
padrão { ação }]
' arquivo
```

Há apenas dois tipos de dados em Awk: números e strings.

## Pares padrão-ação
```
$ awk '$3 > 0 { print $1, $2 * $3}' emp.data
Kathy 40
Susie 76.5
Tanna 109.25
```

O "programa" acima é um par padrão-ação:
- Padrão: 3ro campo maior que zero.
- Ação: Imprimir o 1ro, e o produto do 2do com o 3ro.

Expressões separadas por vírgula, num print, são imprimidas separadas por um espaço em branco " ", e o resultado termina com um "\n" (sinaliza nova linha). [ALT]

**Cada linha de entrada é "testada" por cada padrão**; se "passar", a respectiva ação é executada -- e isso até que se esgotem as linhas de entrada. A própria ordem dos pares padrão-ação num programa em Awk facilita (ou dificulta) chegar ao resultado pretendido.

- Se a ação dum par for omitida, (caso "passe no teste") a linha é imprimida.
- Se o padrão dum par for omitido, a ação ocorre em toda linha.


## A variável NF
Awk conta o número de campos do bloco em causa, e o guarda na variável NF. Assim, se NF é o total, $NF refere-se ao último campo, $(NF-n) ao n-anteúltimo:

```
$ awk 'NF > 0 { print NF, $1, $(NF-1) }' emp.data
3 Xeth 4.00
3 Kathy 4.00
3 Susie 4.25
3 Tanna 5.75
3 Yka 4.00
```

## A variável NR
Awk conta o número de linhas lidas até então, e o guarda na variável NR.
> $ awk '{ print NR, $0 }' emp.data # Prefixa cada linha com o seu respectivo número.


## Inserindo texto na saída
A partir daqui, serão exibidas apenas partes dos programas.

> { print "total pay for", $1, "is", $2 * $3 }
> { printf("total pay for %-8s is $%6.2f\n", $1, $2 * $3) }


## Padrões uteis (para seleção)
### Comparação
> $2 >= 5

### Computação
> $2 * $3 > 50

### Conteúdo textual
> $1 == "Susie" # Imprime linhas nas quais Susie *é* o primeiro campo.

> /Susie/ # Imprime linhas nas quais há Susie nalgum lugar.


## Combinando padrões com && e; || ou; ! não

> $2 >= 4 || $3 >= 20

Linhas que satisfazem ambas condições "passam no teste" apenas uma vez. De fato, é equivalente a isto:

> !($2 < 4 && $3 < 20)


## Validação de dados
O objetivo é "pescar" linhas com propriedades indesejáveis, de modo que, se não ha saída (nada é imprimido), então não há erros.


## Padrões especiais
> BEGIN { ... } # A ação é executada antes de se processar a primeira linha da entrada; é útil para cabeçalhos.

> END { ... }   # ... depois de se processar a ultima; é útil para sumas.


## Computando com Awk
Cada ação e uma sequência de instruções, separadas por linhas ou ";".
> { ; ; ; ; ; ... }

### Contando
```
$3 > 15 { emp = emp + 1 }
END { print emp, "employees worked more than 15 hours" }
```

Observe que variáveis em Awk não precisam ser declaradas. Alem disso, variáveis usadas como números (emp, acima) são inicializadas automaticamente com o valor 0.

```
{ pay = pay + $2 * $3 }
END { print "average pay is", pay/NR }
```

```
$2 > maxrate { maxrate = $2; maxemp = $1 }
END { print "highest hourly rate:", maxrate, "for", maxemp }
```

### Concatenando strings
```
{ names = names $1 " " }
END { print names }
```

Variáveis usadas como strings são automaticamente inicializadas com o valor "" (string vazia). A operação de concatenação em Awk é representada por uma string seguida de outra. Desse modo, o espaçamento comum " ", se pretendido, deve ser explicitado como uma string.


### Funções embutidas
> { print $1, lenght($1) } # lenght conta o número de caracteres numa string

Veja-se a tabela referência de funções embutidas.

### Controle na execução
#### if/else
```
$2 > 6 { n = n + 1; pay = pay + $2 * $3 }
END	{ if (n > 0)
 		print n, "employees, total pay is", pay,
 		"average pay is", pay/n
 	else
 		print "no employees are paid more than $6/hour"
}
```


#### while
```
# Nota: valor=deposito(1+taxa)^anos e a formula usada para
# calcular o valor obtido pelo deposito (investimento) num 
# banco que paga ao cliente um incremento sobre o valor em
# causa, a uma taxa fixa, conforme o período de tempo.
{	i = 1
	while (i <= $3) {
		printf("\t%.2f\n", $1 * (1 + $2) ^ i)
		i = i + 1
	}
}
```
Compare
1000 .06 5
1000 .12 5

#### for
```
{	for (i = 1; i <= $3; i = i + 1)
		printf("\t%.2f\n", $1 * (1 + $2) ^ i)
}
```
Note que não há {} ao for. Isso é legitimo caso o bloco possua apenas uma instrução.


### Arrays
```
{ line[NR] = $0 }	# grava cada linha no array, em ordem (NR)
END { i = NR		# imprime ao contrario
		while (i > 0) {
			print line[i]
			i = i - 1
		}
	}
```

# Awk numa única linha (exemplos)
1. Conta o número de linhas de entrada (LsE)
> END { print NR }

2. Imprime a decima LE
> NR == 10

3. Imprime o último campo de cada LE
> { print $NF }

4. Imprime o último campo da ultima LE
> { field = $NF }
> END { print field }

5. Imprime toda LE com mais de 4 campos
> NF > 4

6. Imprime toda LE na qual o valor do último campo e maior do que 4
> $NF > 4

7. Imprime o número total de campos
> { nf = nf + NF }
> END { print nf }

8. Imprime o número de LsE que contem "Beth"
> /Beth/ { nlines = nlines + 1 }
> END { print nlines }

9. Imprime a LE cujo 1ro campo é o maior (prefixa com o campo em causa). Note que, ao comparar strings, a ordem alfabética comum e avaliada, letra por letra. Assim, Xeth > Susie.
> $1 > max { max = $1; maxline = $0 }
> END { print max, maxline }

10. Imprime LsE com ao menos 1 campo
> NF > 0

11. Imprime LsE cujo número de caracteres é superior a 80
> length($0) > 80

12. Prefixa cada LE com seu respectivo número de campos
> { print NF, $0 }

13. Imprime os 2 primeiros campos de cada LE, ao contrario
> { print $2, $1 }

14. O mesmo que o 13., mas usando uma variável temporária. O ponto é mostrar que a mudança na ordem dos campos ocorreu, de fato, no bloco em causa, enquanto que no item 13. o bloco permaneceu inalterado.
> { temp = $1; $1 = $2; $2 = temp; print $1, $2 }

15. Substitui o 1ro campo de cada LE pelo número da linha em causa
> { $1 = NR; print }

16. Apaga o 2do campo de cada LE (substitui-o pela string vazia)
> { $2 = ""; print }

17. Imprime, ao contrario, os campos de cada LE
> { for (i = NF; i > 0; i = i - 1) printf("%s ", $i)
>	printf("\n")
> } 

18. Substitui campos (numéricos) pelos respectivos módulos
> { for (i = 1; i <= NF; i = i + 1) if ($i < 0) $i = -$i
>	print
> }


# Para saber *tudo* sobre Awk
## Quebrando uma instrução em varias linhas
```
{ print \
		$1,		# comentário
		$2,
		$3 }
```

## Padrão por limites
> padrão1, padrão2 { ações }

O teste passa as LsE entre os dois padrões, inclusive. A ação é feita a cada LE em causa: a primeira LE é a que passou pelo padrão1; a ultima, a que passou pelo padrão2 (ou a ultima LE caso nenhuma seguinte tenha passado pelo padrão2).


## A variável FS
Essa variável determina o caractere de separação de campos. Originalmente, ela é " " ou "\t" (comportamento duplo).
> BEGIN { FS = "\t" } # configura comportamento único

Desse modo, campos podem ter espaços em branco " " enquanto são tratados como entidades individuais.


## Operadores
> < <= == != >= > ~ |~

Qualquer expressão pode ser usada como operando de qualquer operador. Ocorre conversão implícita automaticamente.


### Exemplos
> $3/$2 >= 5.0 # Seleciona LsE cujo quociente do 3ro campo pelo 2do é ao menos 5

> $0 >= "M" # Seleciona LsE cujo primeiro caractere é ao menos M (M, N, O, ...)


#### Teste de strings (se numa string está contida certa substring)
> /Asia / # Teste passa se há "Asia " na LE em causa

> $4 ~ /Asia/ # Teste passa se há "Asia" no 4to campo

> $4 |~ /Asia/ # Teste passa se não há "Asia" no 4to campo

Note que `/Asia/` equivale a `$0 ~ /Asia/`.


## Metacaracteres (/regexpr/)
Notação | significado
A       | A
\t      | tab [\b \f \n \r \t \ddd \c]
\\*     | * literal [\ ^ $ . [ ] | ( ) * + ?]
^       | inicio string
$       | final string
.       | qualquer caractere
[ABC]   | A, B ou C. (classe) Dentro da classe, todo caractere tem seu significado literal, exceto "\", "^" no começo e "-" entre dois caracteres.
[A-Za-z]| A, B, ..., Z, a, b, ... ou z (classe limitada)
[^0-9]  | exceto [0-9] (complemento da classe)
A\|B    | A ou B (alternação, menor precedência)
AB      | A seguido imediatamente por B (concatenação, media precedência)
A*      | A's, zero ou mais (repetição, maior precedência)
A+      | A's, um ou mais (repetição, maior precedência)
A?      | um único A ou nenhum (repetição, maior precedência)
(r)     | grupamento
AB+C    | ABC, ABBC, ABB...C
(AB)+C  | ABC, ABABC, ABAB...C

### Exemplos
> /^[0-9]+$/ # Teste passa se ha apenas dígitos na LE em causa

> /^[0-9][0-9][0-9]$/ # Teste passa se ha exatamente 3 dígitos na LE em causa

> /^(\\+|-)?[0-9]+\\.?[0-9]*$/ OU /^[+-]?[0-9]+[.]?[0-9]*$/ # número decimal, sinal opcional, fração opcional

> /^[A-Za-z][A-Za-z0-9]*$/ # Inicia-se com uma letra do alfabeto, que pode ser seguida por letras e números

> /^[A-Za-z]$|^[A-Za-z][0-9]$/ OU /^[A-Za-z][0-9]?$/ # Uma letra, ou uma letra seguida por um número

> $2 |~ /^[0-9]+$/ # O 2do campo não é um número (string numérica)


## Padrões compostos
> $4 == "Asia" && $3 > 500 # Teste passa se o 4to campo for "Asia" e o 3ro for maior que 500

> $4 == "Asia" || $4 == "Europe" # Teste passa se o 4to campo for "Asia" ou "Europe"

> $4 ~ /^(Asia|Europe)$/ # Equivale ao teste acima; usando regexpr

> /Asia/ || /Europe/ OU /Asia|Europe/ # Equivale ao teste acima se os termos apenas ocorrerem num só campo


## A variável FNR
Guarda o número da linha imediatamente lida do arquivo de entrada em causa. Note que NR guarda o número da LE em causa, contando da primeira linha do primeiro arquivo ate a ultima do último. FNR zera-se a cada arquivo.

## A variável FILENAME
Guarda o nome do arquivo em causa.

> FNR == 1, FNR == 5 { print FILENAME ": " $0 } # Imprime as primeiras 5 linhas de cada arquivo de entrada, usando padrão por limites (prefixa-as com o nome do arquivo em causa)

> FNR <= 5 { print FILENAME ": " $0 } # O mesmo que acima, usando comparação


## Ações
As instruções nas ações podem ser o que segue.
Expressões com constantes numéricas ou strings, variáveis, campos, elementos dum array, atribuições, chamada a funções, etc.

- `print lista`
- `printf(formato, lista)`
- `if (expr) instr [else instr]`
- `while (expr) instr`
- `for (expr; expr; expr) instr`
- `for (var in array) instr`
- `do instr while (expr)`
- `break`
- `continue`
- `next	(próxima iteração do loop principal [próxima LE])`
- `exit	(vai ao END. Se dentro do END, termina.)`
- `exit expr`
- `{ instr }`


## A variável OFS
Controla o separador de campos da saída. Não é incomum isto:
> BEGIN { FS = OFS = "\t" }


## Outras variáveis embutidas
Nome   | Dado
-------|-------------------
ARGC   | número argumentos linha de comando
ARGV   | array dos argumentos
RS     | separador registro entrada. "\n", originalmente.
ORS    | separador registro de saída. "\n", originalmente.
OFMT   | formato saída para números. "%.6g", originalmente.
SUBSEP | separador subscript. "\034", originalmente.


## Cria campo adicional nas LsE
> { $(NF+1) = "1000 * $3 / $2"; print }


## Operadores aritméticos
> \+ - * / % ^

Toda operação considera frações (floating point).


## Outros operadores
### Atributivos
> = += -= *= /= %= ^=

### Unários
> \+ -

### Incrementador e decrementador (prefixos e sufixos)
> ++ --


## Valor de verdade duma comparação
> $4 ~ /Asia/

- == 1, se verdadeira
- == 0, se falsa


## Expressão condicional
> expr1 ? expr2 : expr3

- == expr2, se expr1
- == expr3, se expr1 for falsa

> { print ($1 != 0 ? 1/$1 : "$1 is zero, line " NR) } # Imprime o inverso de $1, se $1 não for 0.

## Atribuições em testes
Uma atribuição é uma expressão cujo valor é idêntico ao que é atribuído.
> if ((n = length($0)) > 0) ...


## Funções aritméticas embutidas. Se x, y são expressões:
Função      | Descrição
------------|----------------
sqrt(x)     |
int(x)	    |	parte inteira de x
atan2(y,x)  |	arctan y/x de -pi a pi
cos(x)      |
sin(x)      |
exp(x)	    |	e^x
log(x)	    |	log(e)x
srand(x)    |	x semeador para rand() [do contrario, rand() determinado]
rand()	    |	r (aleatório), 0 <= r < 1


Note-se que:
1. atan2(0, -1) == pi
2. exp(1) == e
3. log(x)/log(10) == log de x na base 10 [mudança de base]
4. srand(), sem argumento, usa o dia e a hora.
5. `randint = int(n * rand()) + 1 # randint e um inteiro entre 1 e n, inclusive`
6. `x = int(x + 0.5) # arredonda x`


## Concatenação de strings
> { print NR ":" $0 }


## Strings como regexpr
> $0 ~ /^[0-9]+$/ OU $0 ~ "^[0-9]+$"

Ou ainda:
```
BEGIN { digits = "^[0-9]+$" }
$0 ~ digits
```

Portanto uma regexpr (string) pode ser construída a partir de componentes menores (substrings concatenadas). Exemplo:
> regexpr = "^(" regexpr1 "|" regexpr2 ")" ... regexprK "$"


Nota: em strings, metacaracteres (das regexpr) devem ser prefixados com "\\\\" se se pretende testar a ocorrência literal do caractere (ou seja, o próprio "\" protegido [porque um nível de proteção é removido quando algo em aspas é avaliado pelo Awk])

> $0 ~ /(\\+|-)[0-9]+/

> $0 ~ "(\\\\+|-)[0-9]+"


## Funções embutidas para strings.
Se r é regexpr; s, t são strings; n, p são inteiros:

Função                | Descrição                                                         | Retorno
----------------------|-------------------------------------------------------------------|---------------------
gsub(r,s)             | o que passa pela r, vira s, em $0                                 | número de substituições feitas
gsub(r,s,t)           | em t, em vez de $0                                                | número de substituições feitas
sub(r,s)              | apenas a primeira ocorrência vira s, em $0                        |
sub(r,s,t)            | apenas a primeira, em t                                           |
index(s,t)            | .                                                                 | 1ra pos de t em s, ou 0 se não há t
length(s)             | .                                                                 | número de caracteres em s
match(s,r)            | testa se algo em s passa pelo r                                   | índice, ou 0; configura, sobre a substring mais a esquerda e mais longa que passou: RLENGTH	(tamanho) e RSTART (índice)
split(s,a)            | separa s usando FS, põe no array a                                | número de campos
split(s,a,fs)         | mesmo, usando fs                                                  | número de campos
sprintf(fmt,expr-list)| .                                                                 | expr-list formatada conforme [a string] fmt
substr(s,p)           | .                                                                 | sufixo de s a partir da pos p, inclusive
substr(s,p,n)         | .                                                                 | substring de s, da pos p a p+n-1 (n e o tamanho da substring)


## Arrays
Subscripts (ou índices) são strings!
```
/Asia/		{ pop["Asia"] += $3 }
/Europe/	{ pop["Europe"] += $3
END		{ print "Asian population is",
			pop["Asia"], "million."
		  print "European population is",
			pop["Europe"], "million."
		}
```

Qualquer expr pode ser usada como subscript numa referencia:
```
BEGIN { FS = "\t" }
      { pop[$4] += $3 }
END   { for (name in pop)
		print name, pop[name] # $4 $3's
      }
```

Nota: a ordem depende da implementação, é imprevisível.


### Verificando a existencia dum subscript
> subscript in A

- == 1, se A[subscript] existir
- == 0, caso não

> if ("Africa" in pop) ...

Nota sobre if: if roda se não-zero ou não-vazio (híbrido)

Nota:
> if (pop["Africa"] != "") ...

Acaba por criar pop["Africa"]. Não o faça.


### Deletando um elemento
> delete array[subscript]

```
for (i in pop)
	delete pop[i] # apaga todo elemento
```

### Simulando arrays multi-D
```
for (i = 1; i <= 10; i++)
	for (j = 1; j <= 10; j++)
		arr[i, j] = 0 # Awk transforma "i, j" em "i" SUBSEP "j"
```

#### Verificando
> if ((i, j) in arr) ...

#### Iterando
```
for (k in arr) ...
	# use split(k,x,SUBSEP)
```

## Funções definidas pelo usuario
```
function name(param-list) {
	instr
	[return {expr}]
}
```

Exemplo:
```
function max(m, n) { # variáveis m, n limitadas ao escopo de max()
	return m > n ? m : n
}
```

Variáveis são passadas por copia, arrays o são por referencia. Assim, arrays são modificados.

Dentro duma função, toda variável [no programa] que não esta na param-list é visível e acessível (comportamento global). Se pretende proteger alguma variável global, o jeito é por seu nome como parâmetro da função. Ainda que não sejam passados parâmetros suficientes, as variáveis ao final da lista de parâmetros são inicializadas com "" e comportam-se como variáveis locais. Separe-as por alguns brancos dos argumentos reais, para fácil leitura.


## Saída
`print(expr-list)` e `printf(fmt, expr-list)` podem ser seguidos por:
- `> file`
- `>> file`
- `| cmd`


> close(file ou cmd)

> system(cmd)


### A vírgula no `print`
> { print $1, $2 } # imprime campos com o separador de campos (OFS); linhas resultantes são separadas com o separador de registros (ORS)

> { print $1 $2 } # imprime a string concatenada; usa apenas ORS


### Formatação do printf, %[-][w][.p]c:
Símbolo | Descrição
--------|--------------
\-      | alinha a esquerda
w	| acomoda ao tamanho w; põe zeros a esquerda (caso numérico)
.p	| string com p caracteres no máximo, ou dígitos com precisão p
c	| controle do tipo da expr, **c pode ser**:
c	| ASCI char
s	| string
d	| número inteiro positivo
e	| número real em "notação cientifica"
f	| número real
g	| conversão entre e e f. Resulta na representação mais concisa, e sem zeros a esquerda
o	| octal
x	| hexadecimal
%	| % literal


### Exemplo de filtragem e separação, com >
```
$3 > 100	{ print $1, $3 >"bigpop" }
$3 <= 100	{ print $1, $3 >"smallpop" }
```

`>` e `>>`: abre o arquivo, que fica aberto ate close(), ou ate o final do programa; cada print escreve ao arquivo aberto. A diferença entre `>` e `>>` é esta: `>` limpa o arquivo ao abri-lo.

> { print($1, $3) > ($3 > 100 ? "bigpop" : "smallpop") } # usa-se () para eliminar ambiguidade

Observe que os nomes dos arquivos para os quais os resultados vão, estão entre "". Do contrario, big e smallpop seriam entendidos como variáveis.

> { print > $1 } # cada LE vai pro arquivo cujo nome e o seu 1ro campo


## Pipes
> print | "cmd [cmd-args]"

O nome desse pipe é "cmd [cmd-args]".


### Fechando arquivos e pipes
> close(file)

> close("cmd [cmd-args]")

Como com arquivos, pipes são abertos uma única vez. Se um arquivo ou pipe for fechado assim, ao reusa-lo será reaberto.


## Entrada
> awk 'prog' data

> cmd | awk 'prog'


## Separador de campos variável (por LE), usando regexpr
> BEGIN { FS = ",[ \t]*|[ \t]+" }

O separador de campos é "," seguida opcionalmente por brancos e tabs, OU 1 ou mais brancos e tabs

Nota: ao mudar FS (do original " "), brancos e tabs em excesso não são descartados. Por outro lado, se pretende usar um único " ", de fato, como separador de campos, use um truque como `FS = "[ ]"`.


## Processando registros com mais de uma linha

(Tratar varias linhas como um bloco individual). `RS = ""` é especial: quer dizer para entender como registro *o que estiver separado por 1 ou mais linhas em branco*.

> BEGIN { RS = ""; FS = "\n" } # cada linha do registro (bloco) vira um campo


## A função getline
Uso                                 | Descrição
------------------------------------|-------------------------------------------------------------------------------------
getline                             | pega próxima LE. separa campos. configura NF, NR, FNR.
getline x                           | põe próxima LE na variável x. incrementa NR e FNR. não lida com campos.
getline <"file" E cmd | getline     | lê do arquivo ou do pipe. não lida com NR e FNR, mas separa campos e configura NF.
getline x <"file" E cmd | getline x | põe em x. não lida com NR, FNR, nem lida com campos


### O valor duma expr getline
É o valor que a função retorna:
- 1, se ok
- 0, se EOF
- -1, se erro (ex.: de leitura)

#### Exemplo: substituindo '#include "file"' pelo conteúdo de file
```
/^#include/ {
	gsub(/"/, "", $2)
	while (getline x <$2 > 0)
		print x
	next
}
{ print }
```

Note que, num while, qualquer valor não-nulo roda o bloco. Cuide para não cair em loop infinito, como feito acima [`getline > 0`].

#### Exemplo: contando usuários logados
```
while ("who" | getline)
	n++
```

#### Exemplo: pondo a data numa variável
> "date" | getline d


## Atribuições de variáveis pela linha de comando
> awk [-Fsep] -f prog a v=1 b

> awk [-Fsep] 'prog' a v=1 b

A variável *v* é configurada depois de a e antes de b (a e b são arquivos de entrada).


## Argumentos da linha de comando
Argumentos são postos no array ARGV, cujo índice varia de 0 a ARGC-1:

Assim, com 0 <= i < ARGC, ARGV[i] é o i-ésimo argumento. ARGV[0] costuma ser o nome do comando (awk).

### Exemplo: imprimindo argumentos
```
BEGIN {
	for (i = 1; i < ARGC; i++)
		printf "%s ", ARGV[i]
	printf "\n"
}
```

### Exemplo: imitando seq
```
BEGIN {
	if (ARGC == 2)
		for (i = 1; i <= ARGV[1]; i++)
			print i
	else if (ARGC == 3)
		for (i = ARGV[1]; i <= ARGV[2]; i++)
			print i
	else if (ARGC == 4)
		for (i = ARGV[1]; i <= ARGV[2]; i += ARGV[3])
			print i
}
```

Note que os exemplos acima ocorrem apenas dentro da ação do BEGIN; nenhum argumento é entendido como arquivo a ser lido, pela ausência de pares padrão-ação seguintes.

Nota: ARGV e ARGC podem ser alterados para manipular os arquivos a serem lidos (ou não) futuramente. O valor "-" num elemento do ARGV significa para ler da entrada padrão (stdin).


## Interagindo com outros programas
### A função system
> system("cmd")

#### Exemplo: inclusao de arquivo
```
$1 == "#include" { gsub(/"/, "", $2); system("cat " $2); next }
		 { print }
```

## Faca do seu programa em Awk um cmd shell
```
awk '
prog
' $*
```

## (Opcoes da linha de comando)
Um jeito é selecionar de ARGV, ao tempo em que se resetam (= "") os valores selecionados. Assim, no array ARGV restara apenas os nomes dos arquivos a serem lidos; noutro array, o que foi selecionado como opção da linha de comando.

# Outros exemplos
## Padrao para impressao (tratamento horizontal)
```
# imprime soma de colunas (entrada numerica)
{ for (i = 1; i <= NF; i++)
  	sum[i] += $i
  if (NF > maxfld)
  	maxfld = NF
}

END { for (i = 1; i <= maxfld; i++)
		printf("%g%s", sum[i], i < maxfld ? "\t" : "\n") # aqui
}
```

## Usando regexpr (e array binario) para tratar entrada mista 
```
# imprime soma de colunas (entrada mista)
NR==1 { nfld = NF # supoe uniformidade. nfld ao END (NF indisponivel)
		for (i = 1; i <= NF; i++)
			numcol[i] = isnum($i) # array binario, com nfld elementos,
			# usado para controlar (*) as computacoes futuras; ele
			# sera composto pela funcao isnum (regexpr) abaixo.
	  }

	  { for (i = 1; i <= NF; i++)
	  		if (numcol[i]) # (*)
	  			sum[i] += $i # outro array guarda a soma das colunas;
	  			# note que, naturalmente, a soma de colunas nao-nume
	  			# ricas e 0.
	  }

END	  { for (i = 1; i <= nfld; i++) {
			if (numcol[i])
				printf("%g", sum[i])
			else
				print("--")
			printf(i < nfld ? "\t" : "\n")
		}
	  }

function isnum(n) { return n ~ /^[+-]?[0-9]+$/ }
```

## Mudanca de escala
```
# porcentagem do total
	{ x[NR] = $1; sum += $1 } # guarda numeros num array
	# ao tempo em que calcula a soma total (ambos ao uso futuro) 
END { if (sum != 0)
		for (i = 1; i <= NR; i++)
			printf("%10.2f %5.1f\n", x[i], 100*x[i]/sum)
 	}
```

## Histograma
```
# escala de 0 a 100
# dispositivo grafico de 10 em 10
	{ x[int($1/10)]++ } # uso do sufixo incrementador para contagem
	# array x tem 11 elementos, cujos indices vao de 0 a 10

END	{ for (i = 0; i < 10; i++)
		print£(" %2d - %2d: %3d %s\n",
			10*i, 10*i+9, x[i], rep(x[i],"*"))
	  printf("100:		%3d %s\n", x[10], rep(x[10],"*"))
	}

function rep(n,s,   t) { # retorna string de n s's
	while (n-- > 0) # uso do sufixo decrementador para controle
		t = t s		# do loop
	return t
}
```

Note como a variavel t foi posta na lista de args da funcao a fim de proteger uma possivel variavel t externa (global).


## Gerando inteiros
```
# 200 inteiros de 0 a 100, quase aleatoriamente
BEGIN { for (i = 1; i <= 200; i++)
			print int(101*rand())
	  }
```
