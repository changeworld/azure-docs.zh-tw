---
title: 創建 Azure 紅帽開放移位 4.3 群集 |微軟文件
description: 使用 Azure 紅帽開放Shift 4.3 創建群集
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, 開移, 阿茲阿羅, 紅帽子, cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398882"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>建立、存取及管理 Azure 紅帽 OpenShift 4.3 叢集

> [!IMPORTANT]
> 請注意,Azure 紅帽 OpenShift 4.3 目前僅在美國東部和美國東部 2 的專用預覽版中可用。 專用預覽接受僅通過邀請。 在嘗試啟用此功能之前,請務必註冊訂閱[:Azure 紅帽開放移位專用預覽註冊](https://aka.ms/aro-preview-register)

> [!NOTE]
> 預覽功能是自助服務,提供時和可用,不在服務級別協定 (SLA) 和有限保修中。 因此,這些功能不適合生產用途。

## <a name="prerequisites"></a>Prerequisites

建立 Azure 紅帽 OpenShift 4.3 叢集需要以下內容:

- Azure CLI 版本 2.0.72 或更高版本
  
- "az aro"延伸

- 包含兩個空子網的虛擬網路,每個子網沒有附加網路安全組。  群集將部署到這些子網中。

- 叢集 AAD 應用程式(用戶端 ID 和機密)和服務`az aro create`主體,或用於 自動為您創建 AAD 應用程式和服務主體的足夠 AAD 許可權。

- RP 服務主體和群集服務主體必須在群集虛擬網路上具有"參與者"角色。  如果在虛擬網路上具有「使用者存取管理員」角色`az aro create`, 將自動為您設定角色分配。

### <a name="install-the-az-aro-extension"></a>安裝「az aro」延伸
擴展`az aro`允許您使用 Azure CLI 直接從命令列建立、訪問和刪除 Azure 紅帽 OpenShift 叢集。

> [!Note] 
> 擴展`az aro`在預覽中是最新的。 它可能在將來的版本中更改或刪除它。
> 要加入加入延伸預覽,`az aro`您需要`Microsoft.RedHatOpenShift`註冊 資源提供程式。
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. 登入 Azure。

   ```console
   az login
   ```

2. 執行以下指令以安裝`az aro`延伸:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. 驗證 ARO 擴展已註冊。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>取得紅帽拉取機密(可選)

紅帽拉取金鑰使群集可以訪問紅帽容器註冊表和其他內容。 使用拉取密鑰是可選的,但建議使用。

要取得您的拉取秘密:

1. 移至 https://cloud.redhat.com/openshift/install/azure/aro-provisioned 。
1. 登錄到您的紅帽帳戶,或使用您的業務電子郵件創建新的紅帽帳戶;接受條款和條件。
1. 選擇 **「下載拉取」機密**。

將*拉-機密.txt*檔保存在安全的地方;每次創建群集時,都將使用該檔。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>建立包含兩個空子網路的虛擬網路

按照以下步驟創建包含兩個空子網的虛擬網路。

1. 設定下列變數。

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. 為群集創建資源組。

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. 建立虛擬網路

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. 向虛擬網路添加兩個空子網。

   ```console
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. 禁用虛擬網路和子網上的專用鏈路服務的網路策略。 這是 ARO 服務訪問和管理群集的要求。

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>建立叢集

運行以下命令以創建群集。

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> 創建群集通常需要大約 35 分鐘。

## <a name="access-the-cluster-console"></a>存取叢集主控台

您可以在 Azure 紅帽 OpenShift 4.3 群集資源下找到群集主`https://console-openshift-console.apps.<random>.<location>.aroapp.io/`控台網址(窗體 )。 執行以下指令以檢視資源:

```console
az aro list -o table
```

您可以使用`kubeadmin`使用者登錄到群集。  執行以下指令以尋找`kubeadmin`使用者的密碼:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>刪除叢集

執行以下命令以刪除群集。

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
