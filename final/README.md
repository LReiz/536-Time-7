# Modelo de Apresentação da Final

# Estrutura de Arquivos e Pastas

A estrutura aqui apresentada é uma simplificação daquela proposta pelo [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science/). Também será aceito que o projeto adote a estrutura completa do Cookiecutter Data Science e isso será considerado um diferencial. A estrutura geral é a seguinte e será detalhada a seguir:

~~~
├── README.md  <- arquivo apresentando a proposta
│
├── data
│   ├── external       <- dados de terceiros em formato usado para entrada na transformação
│   ├── interim        <- dados intermediários, e.g., resultado de transformação
│   ├── processed      <- dados finais usados para a publicação
│   └── raw            <- dados originais sem modificações
│
├── notebooks          <- Jupyter notebooks ou equivalentes
│
├── slides             <- arquivo de slides em formato PDF
│
├── src                <- fonte em linguagem de programação ou sistema (e.g., Orange, Cytoscape)
│   └── README.md      <- instruções básicas de instalação/execução
│
└── images            <- mídias usadas no projeto
~~~

Na raiz deve haver um arquivo de nome `README.md` contendo a apresentação do projeto, como detalhado na seção seguinte.

## `data`

Arquivos de dados usados no projeto, quando isso ocorrer.

## `notebooks`

Testes ou prototipos relacionados ao projeto que tenham sido executados no Jupyter.

## `src`

Projeto na linguagem escolhida caso não seja usado o notebook, incluindo todos os arquivos de dados e bibliotecas necessários para a sua execução. Só coloque código Pyhton ou Java aqui se ele não rodar dentro do notebook.

 Acrescente na raiz um arquivo `README.md` com as instruções básicas de instalação e execução.

## `assets`

Qualquer mídia usada no seu projeto: vídeo, imagens, animações, slides etc. Coloque os arquivos aqui (mesmo que você mantenha uma cópia no diretório do código).


# Projeto Musication

# Equipe Time 7 - TIME7
* `Pedro Henrique Rodrigues de Araújo` - `223382`
* `Vitor Rodrigues Pietrobom` - `245584`
* `Leonardo Almeida Reis` - `239104`

## Resumo do Projeto
> O projeto visa verificar qual a influência da personalidade/hábitos de uma pessoa no gosto musical. Analisando essa relação, o projeto também visa prever outras músicas que determinada pessoa estaria propensa a gostar com base nas músicas que ele já tem interesse.

## Slides da Apresentação
> [Link dos Slides](https://docs.google.com/presentation/d/1r4C2AARPJPqrHkjzw1DNjytIiKkrkri_l9Ge_kmfmHE/edit?usp=sharing)

## Modelo Conceitual

> ![Modelo Conceitual](images/modelo-conceitual.png)

## Modelos Lógicos

> Modelo lógico relacional
~~~
MUSICA(_Id_, Nome, Artista, Popularidade, Explícito, TipoDeMusica)
RELACAO(_IdMusica1_, _IdMusica2_, Peso)
  _IdMusica1_ chave estrangeira -> MUSICA(_Id_)
  _IdMusica2_ chave estrangeira -> MUSICA(_Id_)
~~~


> Modelo de grafos de conhecimento
>
> Ilustração das Classes:
> ![Modelo Lógico de Grafos de Conhecimento](images/modelo-logico-grafo-classes.png)
>
> Exemplo de Instâncias:
> ![Modelo Lógico de Grafos](images/modelo-logico-grafo-instancias.png)

## Dataset Publicado
> Elencar os arquivos/bases preliminares dos datasets serão publicados.
> 
título do arquivo/base | link | breve descrição
----- | ----- | -----
`musicTable1000.csv` | `(data/processed/musicTable1000.csv)` | `Conjunto de dados contendo propriedades sobre 1000 músicas`
`relationTable1000.csv` | `(data/processed/relationTable1000.csv)` | `Conjunto de dados contendo as relações entre 1000 músicas`


> Os arquivos finais do dataset publicado devem ser colocados na pasta `data`, em subpasta `processed`. Outros arquivos serão colocados em subpastas conforme seu papel (externo, interim, raw). A diferença entre externo e raw é que o raw é em formato não adaptado para uso. A pasta `raw` é opcional, pois pode ser substituída pelo link para a base original da seção anterior.
> Coloque arquivos que não estejam disponíveis online e sejam acessados pelo notebook. Relacionais (usualmente CSV), XML, JSON e CSV ou triplas para grafos.
> Este é o conjunto mínimo de informações que deve constar na disponibilização do Dataset, mas a equipe pode enriquecer esta seção.

## Bases de Dados

título da base | link | breve descrição
----- | ----- | -----
`musicoset_metadata.zip` | `https://marianaossilva.github.io/DSW2019/index.html` | `Dataset com algumas informações sobre as músicas. Dentre as informações úteis, temos: artistas, popularidade, conteúdo explícito, tipo de música`
`musicoset_metadata.zip` | `https://www.aicrowd.com/challenges/spotify-million-playlist-dataset-challenge` | `Dataset com um grande conjunto de playlists. Além disso informa quantas e quais músicas existem em cada playlist.`


## Detalhamento do Projeto

> Iniciamos importando o dataset MusicOSet, o qual continha informações como os ids das musicas, seus nomes, populariade e outras propriedades importantes, do nosso drive compartilhado utilizando pandas:


~~~python
drive.mount('/content/drive')
!ls "/content/drive/Shareddrives/Time 7 - BANCO DE DADOS/musicoset_metadata/songs.csv"
df = pd.read_csv("/content/drive/Shareddrives/Time 7 - BANCO DE DADOS/musicoset_metadata/songs.csv", error_bad_lines=False)
~~~

> Depois definimos cada uma das funções que iria influenciar na relação das músicas, estabelecendo como entrada o dataset obtido acima, uma matriz de adjacencias contendo os pesos das relações e o peso que dariamos para a relação em questão, tendo portanto formatos parecidos com este


~~~python
def relation_popularity(dataset, songs_table, weight):
  for i in range(len(songs_table)):
    for j in range(i+1,len(songs_table[i])):
      relation = (dataset['popularity'][i] + dataset['popularity'][j])/200
      songs_table[i][j] += relation*weight
~~~

> Importante notar que cada propriedade tem seu próprio método de obtenção da relação, o a cima por exemplo utiliza a popularidade de ambas as musicas analisadas para compor seu peso de relação

> Após definir as funções, definimos a matriz que utilizariamos e rodamos as funções uma a uma inicialmente, para que fosse possível gerar grafos exclusivos para cada uma das propriedades, resultando em inúmeros grafos. 

> Concluida esta análise inicial, partimos para a análise completa considerando todas as propriedades selecionadas, para isso executamos todas as funções criadas na matriz, conseguindo assim a matriz completa com as relações finais

~~~python
matrix = np.zeros((5000, 5000))
relationExplicit(df,matrix, 1)
relation_popularity(df, matrix, 1)
relation_song_type(df,matrix, 1)
relation_artist(df, matrix, 1)
~~~

> Com isso geramos o grafo completo, gerando também uma imagem para este. Com a matriz também criamos a tabela contendo cada uma das relações para possibilitar a utilização das Queries, juntamente com uma segunda tabela que consistia basicamente da limpeza do MusicOSet original para caber no nosso objetivo.

~~~python
table = []
for i in range(len(matrix)):
  for j in range(len(matrix)):
    if (matrix[i][j] != 0):
      table.append([df['song_name'][i], df['song_name'][j], matrix[i][j] ])
table = pd.DataFrame(table, columns = ['Song_1', 'Song_2','Relation'])
table.to_csv('relationTable5000.csv', sep=',')
~~~


> Coloque um link para o arquivo do notebook, programas ou workflows que executam as operações que você apresentar.


> Se for notebook, ele estará dentro da pasta `notebook`. Se por alguma razão o código não for executável no Jupyter, coloque na pasta `src` (por exemplo, arquivos do Orange ou Cytoscape). Se as operações envolverem queries executadas atraves de uma interface de um SGBD não executável no Jupyter, como o Cypher, apresente na forma de markdown.

## Evolução do Projeto
> * Ideia Inicial: Analisar como as características pessoais de um determinado indivíduo influenciam no seu gosto musical. Com base no gosto dos indivíduos por gêneros musicais, poderíamos verificar quais características tem mais peso na atração por determinado gênero musical.
> 
> * Evolução da Ideia: À partir da relação entre os gêneros musicais construída pela ideia inicial do projeto, planejamos relacionar as músicas entre si. Com essa relação, iríamos criar um sistema de recomendação de músicas.
> 
> * Problemas Encontrados: Os datasets que encontramos não atrelam gênero às músicas, tornando inviável a relação com o dataset inicial de características pessoais dos indivíduos.
> 
> * Ideia Final: Mudamos os parâmetros para criar a relação entre as músicas. Os parâmetros utilizados foram características das próprias músicas, como: artistas, popularidade, conteúdo explícito, tipo de música e números de playlists que 2 músicas apareciam juntas.


## Perguntas de Pesquisa/Análise Combinadas e Respectivas Análises

### Perguntas/Análise com Resposta Implementada

#### Pergunta/Análise 1
> * Quais as músicas que conseguem agradar o maior escopo de pessoas?
>   
>   * Centralidade para identificar de forma clara as músicas com o maior número de relacionamentos e as músicas que têm menores caminhos para a maioria

#### Pergunta/Análise 2
> * Que outras músicas eu provavelmente gostaria?
>   
>   * Com base nas músicas que uma pessoa gosta, podemos verificar que outras músicas tem maior relação com estas e recomendá-las

#### Pergunta/Análise 3
> * As músicas menos ouvidas têm menos relação com outras músicas?
>   
>   * Analogamente às músicas mais requisitadas, podemos verificar quais as músicas menos ouvidas e verificar se elas têm poucas relações com outras músicas

### Perguntas/Análise Propostas mas Não Implementadas

#### Pergunta/Análise 1
> * Quais as músicas mais singulares?
>   
>   * Centralidade para identificar de forma clara as músicas com o maior número de relacionamentos e as músicas que têm menores caminhos para a maioria

#### Pergunta/Análise 2
> * Qual a relação entre músicas com conteúdo explícito e não explícito?
>   
>   * Músicas com conteúdo explícito tendem a ter menos relação com músicas não explícitas? Ou isso não é um fator tão relevante?

#### Pergunta/Análise 3
> * Que características das músicas são mais requisitadas?
>   
>   * Existem características que os artistas podem focar na produção da suas músicas para fazerem mais sucesso?


> Coloque um link para o arquivo do notebook que executa o conjunto de queries. Ele estará dentro da pasta `notebook`. Se por alguma razão o código não for executável no Jupyter, coloque na pasta `src`. Se as queries forem executadas atraves de uma interface de um SGBD não executável no Jupyter, como o Cypher, apresente na forma de markdown.
