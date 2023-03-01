---
id: install-gcp
title: implantar nós do Polygon no Google Cloud
sidebar_label: Google Cloud Deployment
description: implantação simples dos seus nós do Polygon no Google Cloud
keywords:
- docs
- polygon
- deploy
- nodes
- gcp
- google cloud
slug: install-gcp
image: https://wiki.polygon.technology/img/polygon-wiki.png
---
import useBaseUrl from '@docusaurus/useBaseUrl';

# implantar nós do Polygon no Google Cloud {#deploy-polygon-nodes-on-google-cloud}

Neste documento, descreveremos como implantar nós do Polygon numa instância de VM no Google Cloud.

### Requisitos de hardware {#hardware-requirements}

Verifique os [requisitos](/docs/maintain/validate/validator-node-system-requirements) de hardware mínimos e recomendados no Polygon Wiki.

### Requisitos de software {#software-requirements}

Use qualquer Debian ou Ubuntu Linux OS moderno com suporte a longo prazo, isto é. Debian 11, Ubuntu 20.04. Vamos concentrar-nos no Ubuntu 20.04 neste manual

## implantar a instância (2 formas) {#deploy-instance-2-ways}

Pode utilizar pelo menos duas formas para criar uma instância no Google Cloud:

* gcloud cli, local ou [Cloud Shell](https://cloud.google.com/shell)
* Consola web

Vamos abordar o primeiro caso neste manual. Vamos começar com a implantação utilizando CLI:
1. Siga a [secção "Antes de começar"](https://cloud.google.com/compute/docs/instances/create-start-instance#before-you-begin) para instalar e configurar a linha de comando da ferramenta gcloud.
Preste atenção à região e zona padrão, escolha as mais próximas de si ou dos seus clientes. Pode usar [gcping.com](https://gcping.com) para medir a latência a fim de escolher o local mais próximo.
2. Ajuste as seguintes variáveis de comando ao usar quando necessário o seu editor favorito antes da execução
   * `POLYGON_NETWORK` - escolha `mainnet` ou `mumbai` rede testnet para executar
   * `POLYGON_NODETYPE` - escolha `archive`,`fullnode` tipo de nó para executar
   * `POLYGON_BOOTSTRAP_MODE` - escolha o modo bootstrap `snapshot` ou `from_scratch`
   * `POLYGON_RPC_PORT` - escolha a porta nó BOR JSON RPC para ouvir, o valor padrão é o que é utilizado na criação da instância de VM e nas regras de firewall
   * `EXTRA_VAR` - escolha os campos BOR e Heimdall, use `network_version=mainnet-v1` com rede `mainnet` e `network_version=testnet-v4` com rede `mumbai`
   * `INSTANCE_NAME` - o nome de uma instância de VM com Polygon que vamos criar
   * `INSTANCE_TYPE` - GCP [tipo de máquina](https://cloud.google.com/compute/docs/machine-types), valor padrão é recomendado. Pode alterar mais tarde se necessário
   * `BOR_EXT_DISK_SIZE` - tamanho adicional do disco em GB para utilizar com BOR, valor padrão com `fullnode` é recomendado. Pode expandir mais tarde se necessário. No entanto, vai precisar de 8192GB+ com nó `archive`
   * `HEIMDALL_EXT_DISK_SIZE` - tamanho adicional do disco em GB para usar com Heimdall, valor padrão é recomendado
   * `DISK_TYPE` - GCP [tipo de disco](https://cloud.google.com/compute/docs/disks#disk-types), SSD é altamente recomendado. Pode ser necessário aumentar a quota total de SSD GB na região em que se está a girar o nó.

3. Use o seguinte comando para criar uma instância com requisitos de hardware e software corretos. No exemplo abaixo, nós implantamos Polygon `mainnet` de `snapshot` com modo `fullnode`:
```bash
   export POLYGON_NETWORK=mainnet
   export POLYGON_NODETYPE=fullnode
   export POLYGON_BOOTSTRAP_MODE=snapshot
   export POLYGON_RPC_PORT=8747
   export GCP_NETWORK_TAG=polygon
   export EXTRA_VAR=(bor_branch=v0.3.3 heimdall_branch=v0.3.0  network_version=mainnet-v1 node_type=sentry/sentry heimdall_network=${POLYGON_NETWORK})
   gcloud compute firewall-rules create "polygon-p2p" --allow=tcp:26656,tcp:30303,udp:30303 --description="polygon p2p" --target-tags=${GCP_NETWORK_TAG}
   gcloud compute firewall-rules create "polygon-rpc" --allow=tcp:${POLYGON_RPC_PORT} --description="polygon rpc" --target-tags=${GCP_NETWORK_TAG}
   export INSTANCE_NAME=polygon-0
   export INSTANCE_TYPE=e2-standard-8
   export BOR_EXT_DISK_SIZE=1024
   export HEIMDALL_EXT_DISK_SIZE=500
   export DISK_TYPE=pd-ssd
   gcloud compute instances create ${INSTANCE_NAME} \
   --image-project=ubuntu-os-cloud \
   --image-family=ubuntu-2004-lts \
   --boot-disk-size=20 \
   --boot-disk-type=${DISK_TYPE} \
   --machine-type=${INSTANCE_TYPE} \
   --create-disk=name=${INSTANCE_NAME}-bor,size=${BOR_EXT_DISK_SIZE},type=${DISK_TYPE},auto-delete=no \
   --create-disk=name=${INSTANCE_NAME}-heimdall,size=${HEIMDALL_EXT_DISK_SIZE},type=${DISK_TYPE},auto-delete=no \
   --tags=${GCP_NETWORK_TAG} \
   --metadata=user-data='
   #cloud-config

   bootcmd:
   - screen -dmS polygon su -l -c bash -c "curl -L https://raw.githubusercontent.com/maticnetwork/node-ansible/master/install-gcp.sh | bash -s -- -n '${POLYGON_NETWORK}' -m '${POLYGON_NODETYPE}' -s '${POLYGON_BOOTSTRAP_MODE}' -p '${POLYGON_RPC_PORT}' -e \"'${EXTRA_VAR}'\"; bash"'
```
A instância deve ser criada durante alguns minutos

## Login para a instância (opcional) {#login-to-instance-optional}

Demorará alguns minutos para instalar todo o software necessário e algumas horas para descarregar um snapshot quando escolhido.
Deve ver a trabalhar processos `bor` e `heimdalld` de preenchimento de drives adicionais. Pode executar os seguintes comandos para verificar.
Conectar a instância de serviço SSH usando `gcloud` SSH wrapper:
```bash
gcloud compute ssh ${INSTANCE_NAME}
# inside the connected session
sudo su -

ps uax|egrep "bor|heimdalld"
df -l -h
```
Pode utilizar o seguinte comando para observar o progresso da instalação, é realmente útil em caso de `snapshot` bootstrap
```bash
# inside the connected session
screen -dr
```
Use a combinação de chave `Control+a d` para desconectar da revisão de progresso.

Pode usar os seguintes comandos para obter registos BOR e Heimdall:
```bash
# inside the connected session
journalctl -fu bor
journalctl -fu heimdalld
```
:::note

Dados da blockchain são guardados em drives adicionais que são mantidas por padrão na remoção da instância de VM. Necessita de remover discos adicionais manualmente se já não precisar destes dados.

:::

No final irá receber uma instância como mostrado no diagrama abaixo:<img src={useBaseUrl("img/mainnet/polygon-instance.svg")} />

Se encontrar um problema com este manual - não hesite em registar um [problema](https://github.com/maticnetwork/matic-docs/issues) ou [criar um PR](https://github.com/maticnetwork/matic-docs/pulls) no [GitHub](https://github.com/maticnetwork/matic-docs).
