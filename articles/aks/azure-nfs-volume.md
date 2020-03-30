---
title: 創建 NFS（網路檔案系統）Ubuntu 伺服器，供 Azure 庫伯奈斯服務 （AKS） 的 Pod 使用
description: 瞭解如何手動創建 NFS Ubuntu Linux 伺服器卷，以便與 Azure Kubernetes 服務 （AKS） 中的窗格一起使用
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: e5676710bc47557318f3e2adcf36ec0ed13d47de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596618"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>手動創建和使用具有 Azure 庫伯奈斯服務 （AKS） 的 NFS（網路檔案系統）Linux 伺服器卷
容器之間共用資料通常是基於容器的服務和應用程式的必要元件。 您通常具有需要訪問外部持久卷上相同資訊的各種窗格。    
雖然 Azure 檔是一個選項，但在 Azure VM 上創建 NFS 伺服器是另一種形式的持久共用存儲。 

本文將介紹如何在 Ubuntu 虛擬機器上創建 NFS 伺服器。 還為您的 AKS 容器提供對此共用檔案系統的存取權限。

## <a name="before-you-begin"></a>開始之前
本文假定您有一個現有的 AKS 群集。 如果需要 AKS 群集，請參閱[使用 Azure CLI][aks-quickstart-cli]或使用[Azure 門戶的][aks-quickstart-portal]AKS 快速入門。

您的 AKS 群集需要與 NFS 伺服器位於相同或對等的虛擬網路中。 群集必須在現有 VNET 中創建，VNET 可以與 VM 相同。

本文檔介紹了使用現有 VNET 進行配置的步驟：[在現有 VNET 中創建 AKS 群集][aks-virtual-network]，[並將虛擬網路與 VNET 對等互連連接起來][peer-virtual-networks]

它還假定您創建了 Ubuntu Linux 虛擬機器（例如，18.04 LTS）。 設置和大小可以符合您的喜好，並且可以通過 Azure 進行部署。 有關 Linux 快速入門，請參閱[Linux VM 管理][linux-create]。

如果首先部署 AKS 群集，Azure 將在部署 Ubuntu 電腦時自動填滿虛擬網路欄位，使它們在同一個 VNET 中運行。 但是，如果您要改用對等網路，請參閱上面的文檔。

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>將 NFS 伺服器部署到虛擬機器
下面是在 Ubuntu 虛擬機器中設置 NFS 伺服器的腳本：
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
伺服器將重新開機（由於腳本），您可以將 NFS 伺服器裝載到 AKS。

>[!IMPORTANT]  
>請確保將AKS_SUBNET替換為群集中的正確**AKS_SUBNET，** 否則"*"將打開 NFS 伺服器到所有埠和連接。

創建 VM 後，將上面的腳本複製到檔中。 然後，您可以使用以下功能將其從本地電腦或腳本移動到 VM 中： 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
腳本進入 VM 後，可以進入 VM 並通過以下命令執行它：
```console
sudo ./nfs-server-setup.sh
```
如果執行由於許可權被拒絕錯誤而失敗，則通過命令設置執行許可權：
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>將 AKS 群集連接到 NFS 伺服器
我們可以通過預配持久卷和持久卷聲明來將 NFS 伺服器連接到群集，這些聲明指定如何訪問卷。

連接同一或對等虛擬網路中的兩個服務是必要的。 有關在同一 VNET 中設置群集的說明：[在現有 VNET 中創建 AKS 群集][aks-virtual-network]

一旦他們位於同一虛擬網路（或對等），您需要在 AKS 群集中預配持久性卷和持久性卷聲明。 然後，容器可以將 NFS 磁碟機裝載到其本地目錄。

下面是持久卷的 Kubernetes 定義示例（此定義假定群集和 VM 位於同一 VNET 中）：

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
將**NFS_INTERNAL_IP** **、NFS_NAME**和**NFS_EXPORT_FILE_PATH**替換為 NFS 伺服器資訊。

您還需要一個持久的卷聲明檔。 下面是要包括的內容的示例：

>[!IMPORTANT]  
>**"存儲ClassName"** 需要保留一個空字串，否則聲明將無法正常工作。

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
如果無法從群集連接到伺服器，則問題可能是匯出的目錄或其父目錄沒有足夠的許可權來訪問伺服器。

檢查匯出目錄及其父目錄均具有 777 許可權。

您可以通過運行下面的命令來檢查許可權，目錄應具有 *"drwxrwxrwx"* 許可權：
```console
ls -l
```

## <a name="more-information"></a>詳細資訊
要獲得完整的演練或説明您調試 NFS 伺服器設置，下面是一個深入教程：
  - [NFS 教程][nfs-tutorial]

## <a name="next-steps"></a>後續步驟

有關相關的最佳實踐，請參閱[AKS 中存儲和備份的最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
