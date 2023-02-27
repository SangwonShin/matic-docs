---
id: install-gcp
title: Google 클라우드에서 Polygon 노드를 배포하기
sidebar_label: Google Cloud Deployment
description: Google 클라우드에서 Polygon 노드의 간단한 배포
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

# Google 클라우드에 Polygon 노드 배포하기 {#deploy-polygon-nodes-on-google-cloud}

이 문서에서 GoogleCloud의 VM 인스턴스를 어떻게 배포하는지 설명합니다.

### 하드웨어 요건 {#hardware-requirements}

Polygon Wik에서 최소 및 권장 [하드웨어 요구 사항을](/docs/maintain/validate/validator-node-system-requirements) 확인하십시오.

### 소프트웨어 요건 {#software-requirements}

장기적인 지원이 되는 최신 Debian 또는 Ubuntu Linux OS(즉, Debian 11, Ubuntu 20.04)를 사용하시기 바랍니다. 이번 매뉴얼에서는 Ubuntu 20.04를 중점으로 다루겠습니다.

## 인스턴스 배포(2가지 방법) {#deploy-instance-2-ways}

Google 클라우드에서 인스턴스를 생성하려면 최소 다음 2가지 방법을 사용할 수 있습니다.

* Google Cloud CLI, 로컬 또는 [Cloud Shell](https://cloud.google.com/shell)
* 웹 콘솔

이번 매뉴얼의 첫번째 사례를 다루겠습니다. CLI를 사용한 배포부터 시작하겠습니다.
1. ['시작하기 전에' 섹션](https://cloud.google.com/compute/docs/instances/create-start-instance#before-you-begin)을 따라 Google Cloud 명령줄 도구를 설치하고 구성하세요.
기본 지역 및 영역에 유의하시고 여러분 또는 여러분의 고객에게 최대한 가까운 곳을 선택하세요. [gcping.com](https://gcping.com)을 사용하면 지연 시간을 측정하여 가장 가까운 위치를 선택할 수 있습니다.
2. 필요한 경우, 이전에 사용하던 선호하는 편집기를 사용하여 다음 명령어 변수를 조정하시기 바랍니다
   * `POLYGON_NETWORK` - `mainnet` 또는 `mumbai`테스트넷 네트워크를 선택하여 실행
   * `POLYGON_NODETYPE` - 실행할 노드 유형 `archive`,`fullnode` 선택
   * `POLYGON_BOOTSTRAP_MODE` - 부트스트랩 모드 `snapshot` 또는 `from_scratch` 선택
   * `POLYGON_RPC_PORT` - 수신할 JSON RPC Bor 노드 포트 선택, 기본값은 VM 인스턴스 생성 및 방화벽 규칙에서 사용되는 것
   * `EXTRA_VAR` - Bor 및 Heimdall 브랜치를 선택, `mainnet`네트워크에 `network_version=mainnet-v1`를 이용하고 `mumbai`네트워크에 `network_version=testnet-v4`를 이용
   * `INSTANCE_NAME` - Polygon으로 생성할 VM 인스턴스의 이름
   * `INSTANCE_TYPE` - GCP [머신 유형](https://cloud.google.com/compute/docs/machine-types), 기본값을 권장하며, 필요하면 나중에 교체 가능
   * `BOR_EXT_DISK_SIZE` - Bor와 함께 사용할 GB의 추가 디스크 사이즈, `fullnode`과 함께 기본값을 권장하며, 필요하면 나중에 확장 가능 `archive` 노드와 함께 8192GB+가 필요
   * `HEIMDALL_EXT_DISK_SIZE` - Heimdall과 함께 사용하기 위한 GB 내 추가 디스크 사이즈, 기본값을 권장
   * `DISK_TYPE` - GCP [디스크 유형](https://cloud.google.com/compute/docs/disks#disk-types), SSD를 적극 권장 노드가 실행 중인 지역에서 SSD GB의 총할당량을 늘려야 할 수도 있음

3. 다음 명령어를 사용하여 올바른 하드웨어 및 소프트웨어 요건을 갖춘 인스턴스를 생성합니다. 아래 예시에서는 `snapshot`에서 `fullnode` 모드로 Polygon `mainnet`을 배포합니다.
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
인스턴스는 몇 분 내로 생성되어야 합니다

## 인스턴스 로그인(선택 사항) {#login-to-instance-optional}

모든 필수 소프트웨어를 설치하는 데에는 수 분이 소요될 예정이며 선택 시 스냅샷 다운로드에 수 시간이 소요될 예정입니다.
 `bor`과 `heimdalld` 작업 프로세스가 추가 드라이브를 채우는 것을 확인할 수 있습니다. 다음 명령어를 실행하여 확인할 수 있습니다.
`gcloud` SSH 래퍼를 사용하여 인스턴스 SSH 서비스에 연결
```bash
gcloud compute ssh ${INSTANCE_NAME}
# inside the connected session
sudo su -

ps uax|egrep "bor|heimdalld"
df -l -h
```
다음 명령어를 사용하여 설치 진행을 볼 수 있는데, `snapshot` 부트스트랩의 경우에는 정말 편리합니다.
```bash
# inside the connected session
screen -dr
```
진행 과정 검토를 연결 종료하려면 `Control+a d` 키 조합을 사용합니다.

Bor 및 Heimdall 로그를 보려면 다음 명령어를 사용할 수 있습니다.
```bash
# inside the connected session
journalctl -fu bor
journalctl -fu heimdalld
```
:::note

블록체인 데이터는 VM 인스턴스 제거 시 기본으로 유지되는 추가 드라이브에 저장됩니다. 해당 데이터가 더 이상 필요 없을 경우, 수동으로 추가 디스크를 제거해야 합니다.

:::

마지막에 다음 다이어그램에서 나와 있는 installed installed installation for installed with the execution of the script.<img src={useBaseUrl("img/mainnet/polygon-instance.svg")} />

이 매뉴얼에 문제가 있는 경우 - [GitHub](https://github.com/maticnetwork/matic-docs)에서 [문제](https://github.com/maticnetwork/matic-docs/issues) 또는 [PR 생성](https://github.com/maticnetwork/matic-docs/pulls)을 여세요.
