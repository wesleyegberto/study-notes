# Libs


### Numpy

### Pandas

### Sklearn



### Nltk

Acron: Natural Languague Tool Kit
Lib para processamento de linguagem natural.
É preciso baixar os módulos que serão usados separadamente.

`nltk.download()` ou `nltk.download('stopwords')`
Abre a interface para download dos módulos ou baixa o módulo passado.

Módulos:

* **punkt**
  * Lida com pontuações.
  * Métodos:
    * `nltk.tokenize.word_tokenize(text)`
      * Tokeniza as palavras e pontuações.

* **stopwords**:
  * Lida com palavras sem muito significado.
  * Métodos:
    * `nltk.corpus.stopwords.words("portuguese")`
      * Solicita as palavras stopwords da lingua portuguesa.
* **rslp**:
  * Acron: Removedor Sufixo da Lingua Portuguesa;
  * Lida com as raizes das palavras da lingua portuguesa;
  * Consegue tratar substativos (singular, plural, masculino e feminino) e verbos (e suas conjulgações).
  * Métodos:
    * `stemmer = nltk.stem.RSLPStemmer()`
      * Cria o extrator da raiz das palavras.
  * Métodos do extrator:
    * `stemmer.stem("amigos")`
      * Retorna a raiz da palavra recebida.
* **punkt**:
  * Lida com pontuações.
