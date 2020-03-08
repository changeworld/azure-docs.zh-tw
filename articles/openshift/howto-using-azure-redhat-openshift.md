---
title: 建立 Azure Red Hat OpenShift 4.3 叢集 |Microsoft Docs
description: 使用 Azure Red Hat OpenShift 3.11 建立叢集
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro、openshift、az aro、red hat、cli
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899304"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>建立、存取和管理 Azure Red Hat OpenShift 4.3 叢集

> [!IMPORTANT]
> Azure Red Hat OpenShift （ARO）4.3 提供預覽。 預覽功能是自助服務，並依原樣提供，並會在服務等級協定（SLA）和有限擔保中予以排除。 因此，這些功能不適用於生產用途。

## <a name="prerequisites"></a>必要條件

建立 Azure Red Hat OpenShift 4.3 叢集時，您需要下列各項：

- Azure CLI 2.0.72 或更高版本
  
- ' Az aro ' 延伸模組

- 一個虛擬網路，其中包含兩個空白子網，每個都不會附加網路安全性群組。  您的叢集將會部署到這些子網。

- 叢集 AAD 應用程式（用戶端識別碼和密碼）和服務主體，或足夠的 AAD 許可權，可 `az aro create` 自動為您建立 AAD 應用程式和服務主體。

- RP 服務主體和叢集服務主體必須具有叢集虛擬網路上的「參與者」角色。  如果您在虛擬網路上有「使用者存取系統管理員」角色，`az aro create` 會自動為您設定角色指派。

### <a name="install-the-az-aro-extension"></a>安裝 ' az aro ' 延伸模組
`az aro` 延伸模組可讓您直接從命令列使用 Azure CLI 建立、存取和刪除 Azure Red Hat OpenShift 叢集。

> [!Note] 
> `az aro` 延伸模組貨幣為預覽狀態。 在未來版本中可能會變更或移除。
> 若要加入 `az aro` 延伸模組預覽，您必須註冊 `Microsoft.RedHatOpenShift` 資源提供者。
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. 登入 Azure。

   ```console
   az login
   ```

2. 執行下列命令來安裝 `az aro` 延伸模組：

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. 確認已註冊 ARO 延伸模組。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>建立包含兩個空白子網的虛擬網路

請遵循下列步驟來建立包含兩個空白子網的虛擬網路。

1. 設定下列變數。

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. 建立叢集的資源群組。

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

4. 將兩個空白子網新增至您的虛擬網路。

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

5. 在您的虛擬網路和子網上停用私人連結服務的網路原則。 這是 ARO 服務存取和管理叢集的必要條件。

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>建立叢集

執行下列命令來建立叢集。

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> 建立叢集通常需要大約35分鐘的時間。

## <a name="access-the-cluster-console"></a>存取叢集主控台

您可以在 Azure Red Hat OpenShift 4.3 叢集資源下找到叢集主控台 URL （格式 `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`）。 執行下列命令以查看資源：

```console
az aro list -o table
```

您可以使用 `kubeadmin` 使用者來登入叢集。  執行下列命令來尋找 `kubeadmin` 使用者的密碼：

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>刪除叢集

執行下列命令來刪除叢集。

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```