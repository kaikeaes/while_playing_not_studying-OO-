<!--  se voce ta no vscode, aperte ctrl + k, solte as duas teclas e rapidamente aperte V pra visualizar o texto como deveria -->

# TUTORIALZINHO DA NCURSES (Janelas, movimentação e oq eu aprendi)
> ## A ncurses faz o terminal virar uma matriz, **variavel**, do tamanho do seu terminal. Então ela se comporta quase como um plano cartesiano, tem as coordenadas y e x e voce anda com o cursor e define onde vai começar a digitar algo pra esquerda, do mesmo jeito que voce anda com o seu cursos pra escrever um texto, mas pra isso voce tem que dar todas as informações sobre o lugar que ele tem que se mover, as vezes exato(passando as coordenadas literais) , as vezes relativo de onde ele já estava(se ele já tava em (50,32) vc soma 2 em y e 8 em x pra ficar (52,40) o que faz ele ir 2 pra baixo e 8 pra direito) pra ter a formatação exata de como voce quer as coisas. Isso, de inicio pode ser meio dificil de entender, por isso to fazendo esse texto, pra se voce tiver curiosidade de como faz isso e pro caso de voce ser eu mesmo no futuro que esqueceu de como se usa essa biblioteca, e ta querendo se lembrar como que voce conseguiu fazer um bonequinh zoiudo se movimentar pela tela do terminal º-º

## Compilando o código (-lncurses)
Pra compilar um código usando ncurses são necessárias algumas coisas, estar no Linux( até onde eu saiba n tem pra windows) e ser o super usuário do ambiente ( ou do Pc ou do docker, etc, pra ter ela baixada).
Pra rodar o código com a biblioteca,vc precisa avisar pro compilador q vc ta usando a ela junto com o seu arquivo. No terminal, você tem que colocar o `-lncurses` no final do seu comando. ( eu tbm botaria -o2 tbm pra otimizar o código)
* `ex: gcc codigo.c -o2 PE_JOGO -lncurses`
Se você esquecer disso, vai dar um monte de erro estranho de "undefined reference" (como se as funções não existissem) e não vai compilar nada no final.

> #### a ncurses tem varios comandos, e do mesmo jeito que os comandos da stdio, eles são uma "sigla" pra algo, então, os trechos do texto que tiverem nesse estilo aqui:
## exemplo

> #### `printf - printar formatado`
> #### `scanf - escanear formatado`
> #### `fprintf - "file print" printar formatado em arquivo`
> #### `fgets - "file get string" pega string do arquivo (pode ser o propio stdin)`
---
>### vão explicar essas siglas

## Iniciar e fechar o terminal

### `initscr();`
> #### `iniciar janela`
* não tem parametro
* inicia a ncurses, tem que ser a primeira coisa na main antes de qualquer outra função da biblioteca (menos declaração de variável, defines, etc, ela que fala que voce vai usar o terminal como window).

### `endwin();`
> #### `fechar janela`
* não tem parametro
* fecha a ncurses e deixa o terminal normal, bota no final de tudo pra não bugar o console do pc quando o jogo fechar.
* (se vc der um segfault em algum momento ela provavelmente não vai rodar e o terminal ficará bugado, escreve reset no terminal { n vai aparecer nada escrito} e da enter que ele volta ao normal, acredite, isso já aconteceu mt)

## Coisas a serem utilizadas no inicio

### `noecho();`
> #### `sem "eco", o eco seria voce receber de volta o que voce escreveu`
* não tem parametro
* faz o que você digita não aparecer na tela, precisa pra movimentar o boneco sem encher a tela de 'w', 'a', 's', 'd', ela faz receber input escondido.

### `cbreak()`
> #### `character break, ele já envia oq vc apertou de imediato`
* não tem parametro
* tira o buffer de linha, ou seja, não precisa apertar enter pro programa ler a tecla, ele lê na hora que aperta.

### `curs_set(int <visibilidade>)`
> #### `definir cursor`
* `curs_set(0)` deixa o cursor invisível, `curs_set(1)` deixa normal. é pra esconder aquele tracinho piscando. ( se n me engano tem o `curs_set(2)` que deixa ele mt visivel).

### `keypad(stdscr, <TRUE/FALSE>)`
> #### `teclado`
* ex: `keypad(stdscr, TRUE);`
* habilita ler teclas especiais, tipo as setinhas do teclado (`KEY_UP`, `KEY_DOWN`, `KEY_LEFT`, `KEY_RIGHT`). a `stdscr` é a tela padrão da ncurses.
* ( o scroll do mouse conta como `KEY_UP` e `KEY_DOWN`).

### `nodelay(stdscr, TRUE/FALSE)`
> #### `sem delay, sem espera, sem demora`
* ex: `nodelay(stdscr, TRUE);`
* faz o `getch()` não esperar vc apertar um botão. se não apertar nada, ele ignora e o jogo continua rodando, é o que faz o timer de segundos funcionar independente do cara apertar algo ou não, ou as coisas acontecerem sem voce ficar dando input
* o `getch()` ta la no "Lendo jogador"

### `flushinp()`
> #### `descarga de input`
* não tem parametro
* limpa o buffer do teclado (joga fora as teclas que tão na fila). bom pra quando tem uns dialogos e dps inicia alguma coisa, se vc n der flushinp, qnd a pessoa tentar pular o dialogo ou oq quer que seja, o buffer vai segurar as teclas q ela apertou e jogar tudo de uma vez na proxima tela que aceitar input, ficando meio bugado td.

## Janelas e ASCII(desenhinhos)


### Comandos principais pra printar as coisas (na janela padrão)
Antes de ir pras janelas separadas, a ncurses tem a `stdscr` (standard screen), que é a tela do terminal todo. Tudo o que você faz nela usa os comandos em suas versões base, sem o 'w' na frente.

### `move(int <y>, int <x>)`
> ##### `mover`
* move o cursor pra coordenada y (linha) e x (coluna). Lembra que o ponto 0, 0 é o canto superior esquerdo do terminal, quando vc soma o y voce vai pra baixo e quando soma o x pra direita, pense que voce está no quarto quadrante, se vc gostar de matematica, do contrário ignore.

### `printw(char <texto>, <variavel1>, <variavel2>...)`
> #### `printar na janela`
* ex: `printw("Vidas: %d%%", vida_porcento);`
* funciona exatamente igual ao printf do C, mas ele escreve o texto onde quer que o cursor esteja naquele momento (por isso usamos o move antes).

### `mvprintw(int <y>, int <x>, char <texto>, <variavel1>, <variavel2>...)`
> #### `mover e printar na janela`
* ex: `mvprintw(2, 5, "bombas: %d", bombas);`
* junta o `move()` com o `printw()`. move o cursor pra coordenada y, x na tela principal e já printa o texto la de uma vez.

### `attron(int <atributo>)` e `attroff(int <atributo>)`
> #### `atriuir ativo, atribuir desativado`
* ex: `attron(COLOR_PAIR(42) | A_REVERSE);`
* liga (on) e desliga (off) um atributo na tela inteira, tipo cor, negrito (`A_BOLD`) ou o negativo(`A_REVERSE`). tudo que printar entre o on e o off sai com esse estilo.
* ###  não esqueça de botar um attroff ou wattrof após um wattron ou attron, buga tudo
* ### também não esqueça de manter noção dos numeros dos pares que voce define, pois repetir numeros da uma baita dor de cabeça

### `erase()`
> #### `apagar`
* limpa a tela principal inteira, apaga tudo pra você desenhar o próximo frame por cima sem ficar o rastro do boneco andando. 
* ### `existe o clear ou wclear, não indico, muito pesado comparado com o erase`
>##### a erase substitui os caractreres da tela por " "
>##### já a clear faz isso e ela chama o clearok, o que reseta completamente o estado do terminal, desnecessária, normalmente.

### `refresh()`
> #### `tipo um f5 da vida, atualizar`
* atualiza a tela. você printa tudo que quer na memória com os `printw` ou `mvprintw`, e só quando chama o `refresh` é que a ncurses cospe tudo pro monitor de uma vez.


### `newwin(int <linhas>, int <colunas>, int <y do inicio da janela>, int <x do inicio da janela>)`

> `!!!` tem umas coisas importantes que n sei se eu já falei, só uma pausa aqui, LINES e COLS são valores já definidos no header da ncurses que tem o valor exato do seu terminal no momento ( se vc mudar o tamanho do terminal abrindo alguma aba ou subindo/descendo ele esse valor atualisa junto é muuuuito importante saber isso, pra auxiliar o código ser adaptavel pra varios monitores diferentes)
>
>`!!!` quando voce vai criar uma nova janela, voce pode definir o tamanho dela, o lugar onde ela começa e relação a stdscr etc, indico tomar cuidado pra n fazer ela maior q a stdscr, pq buga as coisas, um exemplo bom de como funciona a criação de janelas com tamanho diferente é a campominado.c ou a cobra.c

voltando:
*  a newwin cria uma janela nova separada da tela inteira (stdscr). 
* ex: `WINDOW *hud = newwin(5, 20, 0, 0);` 
* perfeito pra fazer a HUD, menu ou o tabuleiro isolado.


### `delwin(WINDOW *<janela>)`
* deleta a janela da memória quando não for mais usar, use essa função, n esquece dela n.

### `box(WINDOW *<janela>, int <char_vertical>, int <char_horizontal>)`
* ex: `box(campominado, 0, 0);`
* faz uma bordinha bonitinha em volta da janela. usar 0 e 0 puxa a linha padrão da ncurses.
> vc tbm pode simular uma borda com os caracteres especiasi da ncurses

> ##### `ACS_HLINE` — Linha horizontal
> ##### `ACS_VLINE` — Linha vertical
> ##### `ACS_ULCORNER` — Canto superior esquerdo
> ##### `ACS_URCORNER` — Canto superior direito
> ##### `ACS_LLCORNER` — Canto inferior esquerdo
> ##### `ACS_LRCORNER` — Canto inferior direito
> ##### `ACS_PLUS` — Cruz ou sinal de mais grande
> ##### `ACS_LTEE` — junção em 'T' apontando para a direita 
> ##### `ACS_RTEE` — junção em 'T' apontando para a esquerda 
> ##### `ACS_BTEE` — junção em 'T' apontando para cima 
> ##### `ACS_TTEE` — junção em 'T' apontando para baixo 
> ##### `ACS_DIAMOND` — Diamante
> ##### `ACS_CKBOARD` — Sombra pontilhada / tabuleiro de xadrez 
> ##### `ACS_DEGREE` — Símbolo de grau 
> ##### `ACS_PLMINUS` — Símbolo de mais ou menos 
> ##### `ACS_BULLET` — Ponto/marcador 
> ##### `ACS_LANTERN` — Lanterna       

## LENDO O JOGADOR

### `getch()`
> #### `pegar caractere`
* pega a tecla que o vc apertou na tela principal e manda pra tal variavel.
* ex: `int tecla = getch();`


## COMANDOS EM JANELAS (`W`...)
A ncurses tem um padrão quase todo comando básico da tela principal tem um modo pra uma janela isolada (`newwin`), com um `w` na frente dele (ou logo depois do `mv`) e passar a sua variável da janela como o primeiro parâmetro. 

### `mvwprintw(WINDOW *<janela>, int <y>, int <x>, const char *<texto>, ...)`
> #### `move na janela e printa na janela`
* ex: `mvwprintw(campominado, 2, 5, "bombas: %d", bombas);`
* a mistura do `move()` com `printw()` focado em uma janela,
 o y e x aqui são relativos a onde vc quer o inicio da janela criada n o da tela inteira

### `wattron(WINDOW *<janela>, int <atributo>)` e `wattroff(WINDOW *<janela>, int <atributo>)`
* ex: `wattron(campominado, COLOR_PAIR(42) | A_REVERSE);`
* liga (on) e desliga (off) atributos, mas apenas na janela específica.

### `werase(WINDOW *<janela>)`
> #### `apaga na janela`
* limpa a janela inteira, apaga tudo que tá nela sem mexer no que tá desenhado no resto do monitor.

### `wrefresh(WINDOW *<janela>)`
> #### `atualiza a janela`
* atualiza só essa janela na tela pro jogador ver.

### `wgetch(WINDOW *<janela>)`
> #### `pega o caractere da janela`
* pega a tecla que o cara apertou enquanto o foco tá naquela janela. 
* ex: `int tecla = wgetch(campominado);`

### `keypad(WINDOW *<janela>, <TRUE/FALSE>)`
* ex: `keypad(campominado, TRUE);`
* habilita ler setinhas especificamente pra leitura dessa janela.

### `nodelay(WINDOW *<janela>, bool)`
* ex: `nodelay(cobra, TRUE);`
* faz o `wgetch()` dessa janela específica não travar o jogo esperando botão.


## CORES

### `start_color()`
> #### `iniciar cores`
* não tem parametro
* primeira função pra iniciar as cores (usada de preferencia logo apos o `initscr()`)

### `init_pair(short <id do par>, short <cor do texto>, short <cor do fundo>)`
> #### `inicializar par de cores`
* define a combinação de cor de texto e cor de fundo para ser usada depois com o `COLOR_PAIR(id)`.

### `init_color(short <id da cor>, short <vermelho>, short <verde>, short <azul>)`
> #### `inicializar cor personalizada`
* é tipo um rgb mas vai de 0 a 1000 ao inves dos 255, tudo 1000 é branco tudo 0 é preto.

### `use_default_colors()`
> #### `usar cores padrão`
* não tem parametro, habilita o `-1` nos `init_pair`, faz pegar a cor do terminal.

### `has_colors()`
> #### `tem cores?`
* não tem parametro, retorna valor booleano true ou false, verifica se o terminal tem suporte pra receber cores.