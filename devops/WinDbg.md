# Debugando com WinDbg

### Notação
\# - argumento do comando ou flag

### Arquivos Pdbs
* Contém mapeamento do código nativo para o nosso código


### Comandos

* !sympath
  * setar a pasta com os simbolos.
* !path
  * exibir mais informações sobre o processo (DLLs carregadas, caminhos, variáveis de ambientes carregadas, etc).
* !runaway
  * exibir infos das threads que mais consomem.
* ~#s
  * onde # é o número da thread exibida pelo comando _runaway_;
  * exibir código em execução.
* !verifyheap
  * verificar memória do dump que foi gerado.
* !clrstack
  * exibir stack do CLR;
  * flag _-a_ exibe os valores das variáveis.
* !DumpStack -EE
* !U /d #
* !ip2md #
* !DumpModule
* !DumpMT #
  * Exibe informações de um metatype
* !dumpheap -stat
  * Exibe informações detalhadas sobre o heap do dump;
  * flags:
    * -stat: analisa e lista os objetos da memória por tamanho.
    * -type #: filtra por tipo. 
    * -min #: permite filtrar o tamanho do objeto em memória;
* !DumpObj /d #
  * dump de objeto na memória onde # é o endereço da memória;
* !gcroot #
  * exibe os objetos que referencia o objeto referenciado por #;

### Notas
* Para carregar um dump do .Net de forma ideal é preciso gerar o publish da aplicação com a flag de simbolos (_--include-symbols_) runtime (_-r_ ou _--runtime_), adicionar o path do SOS.dll que está na pasta do publish usando o comando _sympath_.


