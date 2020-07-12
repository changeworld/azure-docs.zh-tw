---
title: 建立 NFS Ubuntu Linux 伺服器磁片區
titleSuffix: Azure Kubernetes Service
description: '瞭解如何在 Azure Kubernetes Service (AKS 中，以手動方式建立用於 pod 的 NFS Ubuntu Linux 伺服器磁片區) '
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251989"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service (AKS，手動建立和使用 NFS (網路檔案系統) Linux 伺服器卷) 
在容器之間共用資料通常是容器型服務和應用程式的必要元件。 您通常會有各種 pod 需要存取外部持續性磁片區上的相同資訊。    
雖然 Azure 檔案服務是一個選項，但在 Azure VM 上建立 NFS 伺服器是另一種形式的持續性共用儲存體。 

本文將說明如何在 Ubuntu 虛擬機器上建立 NFS 伺服器。 也會為您的 AKS 容器提供此共用檔案系統的存取權。

## <a name="before-you-begin"></a>開始之前
本文假設您已有現有的 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您的 AKS 叢集必須位於與 NFS 伺服器相同或對等互連的虛擬網路中。 叢集必須建立在現有的 VNET 中，這可以是與您的 VM 相同的 VNET。

如需使用現有 VNET 進行設定的步驟，請參閱下列檔：在[現有的 vnet 中建立 AKS][aks-virtual-network]叢集和[使用 VNET 對等互連連接虛擬網路][peer-virtual-networks]

它也假設您已建立 Ubuntu Linux 虛擬機器 (例如 18.04 LTS) 。 設定和大小可以是您的喜好，而且可以透過 Azure 部署。 如需 Linux 快速入門，請參閱[LINUX VM 管理][linux-create]。

如果您先部署 AKS 叢集，則在部署您的 Ubuntu 機器時，Azure 會自動填入 [虛擬網路] 欄位，使其存留在相同的 VNET 中。 但是，如果您想要改為使用對等互連網路，請參閱上述檔。

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>將 NFS 伺服器部署至虛擬機器
以下是在您的 Ubuntu 虛擬機器中設定 NFS 伺服器的腳本：
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
伺服器將會因為腳本) 而重新開機 (，而且您可以將 NFS 伺服器掛接到 AKS。

>[!IMPORTANT]  
>請務必將**AKS_SUBNET**取代為叢集的正確一個，否則 "*" 會將您的 NFS 伺服器開啟至所有埠和連線。

建立 VM 之後，請將上述腳本複製到檔案中。 然後，您可以使用下列方式，將它從本機電腦或腳本所在的位置移至 VM： 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
當您的腳本位於 VM 之後，您可以透過 ssh 連線到 VM，並透過命令執行它：
```console
sudo ./nfs-server-setup.sh
```
如果因為許可權拒絕錯誤而導致其執行失敗，請透過命令設定執行許可權：
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>將 AKS 叢集連接到 NFS 伺服器
我們可以藉由布建持續性磁片區和持續性磁片區宣告，將 NFS 伺服器連線到叢集，以指定如何存取磁片區。

需要在相同或對等互連的虛擬網路中連接這兩個服務。 如需在相同 VNET 中設定叢集的指示，請參閱：[在現有的 vnet 中建立 AKS][aks-virtual-network]叢集

一旦它們位於相同的虛擬網路 (或對等互連) ，您就必須在 AKS 叢集中布建持續性磁片區和持續性磁片區宣告。 然後，容器可以將 NFS 磁片磁碟機掛接到其本機目錄。

以下是持續性磁片區的範例 Kubernetes 定義 (此定義會假設您的叢集和 VM 位於相同的 VNET) 中：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
以 NFS 伺服器資訊取代**NFS_INTERNAL_IP**、 **NFS_NAME**和**NFS_EXPORT_FILE_PATH** 。

您也需要一個持續性磁片區宣告檔案。 以下是要包含的範例：

>[!IMPORTANT]  
>**"storageClassName"** 必須保留空白字串，否則宣告將無法運作。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>疑難排解
如果您無法從叢集連接到伺服器，可能是匯出的目錄或其父系沒有足夠的許可權可以存取伺服器。

檢查您的匯出目錄及其上層目錄是否具有777許可權。

您可以藉由執行下列命令來檢查許可權，且目錄應具有 *' drwxrwxrwx '* 許可權：
```console
ls -l
```

## <a name="more-information"></a>更多資訊
若要取得完整的逐步解說，或協助您進行 NFS 伺服器設定的偵錯工具，以下是深入教學課程：
  - [NFS 教學課程][nfs-tutorial]

## <a name="next-steps"></a>後續步驟

如需相關的最佳做法，請參閱 [AKS 中的儲存和備份最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
