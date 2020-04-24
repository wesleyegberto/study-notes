# AWS Storages

## Tipos

### Serviços de Storages

* S3
* S3 Glacier
* Storage Gateway
* EFS
* FSx
* AWS Backup

### Outros Serviços que usam Storage

* ESB
* Storage ElasticSearch: instância ElasticSearch gerenciando que utiliza um disco

## Cobrança

Os serviços de armazenamento, geralmente, são cobrados por:

* valor provisionado (tamanho do armazenamento do volume)
* escrita e leitura de dados

## Detalhes

### S3 - Simple Storage Service

* storage para internet
* multi AZ
* billing pelo uso efeito (GB/mes)
* capacidade ilimitada
* altamente durável
* altamente resiliente (até 99,99%)
* suporta versionamento
* suporta encriptação
* nativamente HTTP (URL exposta na rede privada e/ou pública)
* permite subir um site estático
* utiliza o Glacier para backup
* nome do bucket precisa ser único entre todas as regiões

#### Tiers

* Standard
* Infrequently access
* One zone IA
* Inteligent tiering
* Glacier
* Deep archive

### Glacier

* funciona integrado ao S3
* utilizado para arquivamento
* disco mais baratos da AWS
* 4~12 hrs para recuperação
* recuperação feita pode ser realizada em alguns casos (mais caro)

### Storage Gateway

* appliance (VM ou instância)
* on-premise ou cloud
* apresenta volumes: objetos, iSCSI, VTL (Virtual Tape Library)
* por trás tem um S3 e Glacier
* permite estender o S3 numa rede (praticamente simula um disco)

### EFS - Elastic File System

* NFS gerenciado
* nativamente multi AZ
* billing por uso efeito (GB/mes)
* ideal para aplicaçÕes legada
* não tem suporte no Windows

### FSx - Elastic File System for Windows

* suporte Windows File Server CIFS (SMB)
* suporte Lustre (alta performance)
* ponto de montagem `\\`
* nativamente multi AZ
* integração com AD
* billing por provisionamento

### ESB

* HDs de VMs
* Pode ser attachado e removido
* residem em apenas uma AZ (não permite utilizar de outra zona de disponibilidade)
* suporta snapshots (multi AZ)
* limite de 8 eptabytes/s

#### Snapshot

* cópia fiel do estado armazenado no S3
* algumas aplicações necessitam parada de IO
* billing de forma incremental (diferença entre dois snapshots de um mesmo volume)
* snapshot pode ser usado para criar um volume (AWS faz o gerenciamento do snapshots incremental)
* multi AZ

