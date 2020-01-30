Documentar como se recuperar de possíveis desastres na infraestrutura ou servicos.

# Problema no 3850

# Restaurar backup de VM

# Restaurar nuvem 

# Restaurar 


# Recuperação de desastre

A recuperação de desastre dessa estrutura consiste em seguir os passos desse tutorial a partir de [instânciação-dos-hosts](#instânciação-dos-hosts).

Caso não seja necessário reinstalar o SO, os passos são os seguintes:

Remover o cluster kubernetes:   
``` 
$ ./rke remove 
```

Acessar cada nó e rodar os seguintes comandos:
```
$ sudo docker stop $(docker ps -a -q)
$ sudo docker rm $(docker ps -a -q)
$ sudo docker volume prune
$ sudo rm -rf /var/lib/rook
```

## Reestabelecendo o cluster kubernetes e os serviços:

Quando você já possui acesso via ssh (ssh rancher@nodenuvemX) a todos os hosts pode executar o comando para criar/atualizar o cluster(possuindo o arquivo cluster.yml):

```$ ./rke up --config cluster.yml```

Baseado nas configurações oficiais de [Backups and Disaster Recovery](https://rancher.com/docs/rke/v0.1.x/en/etcd-snapshots/) e [Creating Backups—High Availability Installs](https://rancher.com/docs/rancher/v2.x/en/backups/backups/ha-backups/) configuramos o serviço de snapshots recorrentes do etcd no [cluster.yml](cluster.yml). Os snapshots estão sendo sincronizados diariamente no servidor de monitoramento/backup em /backup/etcd_kubernetes e são gravados em fita. 

Para fazer o restore do backup utilize os passos descritps em [Restoring Backups—High Availability Installs](https://rancher.com/docs/rancher/v2.x/en/backups/restorations/ha-restoration/).

## Caso não seja possível a opção acima, é possivel recolocar os serviços no ar seguindo os passos abaixo:

Clone o repositório dos serviços: https://github.com/ctic-sje-ifsc/servicos_kubernetes

Criando todos os namespaces:
```
$ cd servicos_kubernetes/namespaces
$ kubectl create -f namespaces.yaml
```

Baixar as chaves do [drive](https://drive.google.com/drive/folders/0B_KFdN7OB_xwZ1J0SVk2QWNnU3M?usp=sharing) e criar todas com o comando:

```
$ make -i create
```

Depois acesse a pasta dos serviços e inicie todos com o seguinte comando:
```
$ cd servicos_kubernetes
$ make -i create
```