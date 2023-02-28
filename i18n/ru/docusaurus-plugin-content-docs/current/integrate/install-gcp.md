---
id: install-gcp
title: Развернуть ноды Polygon в Google Cloud
sidebar_label: Google Cloud Deployment
description: Простое развертывание ваших нодов Polygon в Google Cloud
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

# Развернуть узлы Polygon в Google Cloud {#deploy-polygon-nodes-on-google-cloud}

В этом документе мы расскажем, как развернуть узлы Polygon в экземпляр VM в облаке Google.

### Требования к аппаратному обеспечению {#hardware-requirements}

Проверьте минимальные и рекомендуемые [требования к](/docs/maintain/validate/validator-node-system-requirements) оборудованию в Polygon Wiki.

### Требования к программному обеспечению {#software-requirements}

Используйте любую современную ОС Linux(Debian или Ubuntu) с долгосрочной технической поддержкой, например, Debian 11 или Ubuntu 20.04. В этом руководстве рассматривается Ubuntu 20.04.

## Развертывание экземпляра (2 способа) {#deploy-instance-2-ways}

Вам доступны как минимум два способа создания экземпляра в Google Cloud:

* местный интерфейс командной строки Google Cloud или [Cloud Shell](https://cloud.google.com/shell);
* веб-консоль.

В этом руководстве рассматривается первый случай. Начнем с развертывания с помощью интерфейса командной строки:
1. Выполните действия из [раздела Before you begin](https://cloud.google.com/compute/docs/instances/create-start-instance#before-you-begin) (перед началом работы), чтобы установить и настроить инструмент командной строки Google Cloud.
Обратите внимание на регион и часовой пояс, установленные по умолчанию. Выберите те, что ближе к вам или вашим клиентам. Вы можете воспользоваться [gcping.com](https://gcping.com), чтобы измерить уровень задержки и выбрать ближайшее местоположение.
2. При необходимости команд измените следующие переменные команд, используя ваш любимый редактор.
   * `POLYGON_NETWORK` — выберите тестовую сеть `mainnet` или `mumbai` для запуска.
   * `POLYGON_NODETYPE` —- выберите тип нода `archive`, `fullnode` для запуска.
   * `POLYGON_BOOTSTRAP_MODE` — выберите режим начальной загрузки `snapshot` или `from_scratch`.
   * `POLYGON_RPC_PORT` — выберите порт нода Bor протокола JSON-RPC для прослушивания. По умолчанию установлено значение, которое используется при создании экземпляра виртуальной машины и в правилах брандмауэра.
   * `EXTRA_VAR` — выберите ветви Bor и Heimdall. Используйте `network_version=mainnet-v1` для сети `mainnet` и `network_version=testnet-v4` для сети `mumbai`.
   * `INSTANCE_NAME` — название экземпляра виртуальной машины в Polygon, который мы собираемся создать.
   * `INSTANCE_TYPE` — [тип машины](https://cloud.google.com/compute/docs/machine-types) Google Cloud Platform. Рекомендуется использовать значение по умолчанию. При необходимости вы можете изменить его позже.
   * `BOR_EXT_DISK_SIZE` — дополнительное место на диске в ГБ для использования на Bor. Для `fullnode` рекомендуется использовать значение по умолчанию. При необходимости вы можете увеличить его позже. Но для `archive` вам понадобится не менее 8192 ГБ.
   * `HEIMDALL_EXT_DISK_SIZE` — дополнительное место на диске в ГБ для использования на Heimdall. Рекомендуется использовать значение по умолчанию.
   * `DISK_TYPE` — [тип диска](https://cloud.google.com/compute/docs/disks#disk-types) Google Cloud Platfrom. Настоятельно рекомендуется использовать SSD-накопитель. Вам может потребоваться увеличить общую квоту в ГБ для SSD-накопителя в регионе, где разворачивается нод.

3. Чтобы создать экземпляр, отвечающий требованиям к аппаратному и программному обеспечению, используйте следующие команды. В приведенном ниже примере мы развертываем Polygon `mainnet` с помощью моментального снимка `snapshot` в режиме `fullnode`:
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
Создание экземпляра должно занять нескольких минут.

## Вход в систему экземпляра (необязательно) {#login-to-instance-optional}

Установка всего необходимого программного обеспечения займет пару минут, а для загрузки выбранного моментального снимка потребуется несколько часов.
Вы увидите, как запущенные процессы `bor`и `heimdalld` заполняют место на дополнительных дисках. Чтобы это проверить, можно выполнить следующие команды.
Подключитесь к службе SSH экземпляра с помощью оболочки SSH `gcloud`:
```bash
gcloud compute ssh ${INSTANCE_NAME}
# inside the connected session
sudo su -

ps uax|egrep "bor|heimdalld"
df -l -h
```
Чтобы следить за ходом установки, вы можете использовать следующую команду. Это очень удобно в случае начальной загрузки на основе `snapshot`.
```bash
# inside the connected session
screen -dr
```
Чтобы отключить просмотр хода выполнения, используйте сочетание клавиш `Control+a d`.

Для получения журналов Bor и Heimdall можно использовать следующие команды:
```bash
# inside the connected session
journalctl -fu bor
journalctl -fu heimdalld
```
:::note

Данные блокчейна хранятся на дополнительных дисках, которые по умолчанию сохраняются при удалении экземпляра виртуальной машины. Если эти данные вам больше не нужны, дополнительные диски нужно удалить вручную.

:::

В конце вы получите экземпляр, как показано на диаграмме ниже:<img src={useBaseUrl("img/mainnet/polygon-instance.svg")} />

Если при использовании этого руководства у вас возникнут проблемы, вы можете сообщить о [проблеме](https://github.com/maticnetwork/matic-docs/issues) или создать [запрос на включение внесенных изменений](https://github.com/maticnetwork/matic-docs/pulls) на [GitHub](https://github.com/maticnetwork/matic-docs).
