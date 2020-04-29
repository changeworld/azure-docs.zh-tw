---
title: 建立 Azure Red Hat OpenShift 4.3 叢集 |Microsoft Docs
description: 使用 Azure Red Hat OpenShift 4.3 建立叢集
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro、openshift、az aro、red hat、cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398882"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>建立、存取和管理 Azure Red Hat OpenShift 4.3 叢集

> [!IMPORTANT]
> 請注意，Azure Red Hat OpenShift 4.3 目前僅適用于美國東部和美國東部2的個人預覽版。 私人預覽接受僅限邀請。 請務必先註冊您的訂用帳戶，再嘗試啟用此功能： [Azure Red Hat OpenShift 私人預覽註冊](https://aka.ms/aro-preview-register)

> [!NOTE]
> 預覽功能是自助服務，並依原樣提供，並會在服務等級協定（SLA）和有限擔保中予以排除。 因此，這些功能不適用於生產用途。

## <a name="prerequisites"></a>先決條件

建立 Azure Red Hat OpenShift 4.3 叢集時，您需要下列各項：

- Azure CLI 2.0.72 或更高版本
  
- ' Az aro ' 延伸模組

- 一個虛擬網路，其中包含兩個空白子網，每個都不會附加網路安全性群組。  您的叢集將會部署到這些子網。

- 叢集 AAD 應用程式（用戶端識別碼和密碼）和服務主體，或的足夠 AAD `az aro create`許可權，可讓您自動建立 AAD 應用程式和服務主體。

- RP 服務主體和叢集服務主體必須具有叢集虛擬網路上的「參與者」角色。  如果您在虛擬網路上有「使用者存取系統管理員」角色， `az aro create`則會自動為您設定角色指派。

### <a name="install-the-az-aro-extension"></a>安裝 ' az aro ' 延伸模組
此`az aro`延伸模組可讓您直接從命令列使用 Azure CLI，來建立、存取和刪除 Azure Red Hat OpenShift 叢集。

> [!Note] 
> 此`az aro`延伸模組在預覽版中是貨幣。 在未來版本中可能會變更或移除。
> 若要加入宣告`az aro`延伸模組預覽，您需要註冊`Microsoft.RedHatOpenShift`資源提供者。
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. 登入 Azure。

   ```console
   az login
   ```

2. 執行下列命令來安裝`az aro`擴充功能：

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. 確認已註冊 ARO 延伸模組。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>取得 Red Hat pull 密碼（選擇性）

Red Hat 提取密碼可讓您的叢集存取 Red Hat 容器登錄和其他內容。 使用提取密碼是選擇性的，但建議使用。

若要取得您的提取密碼：

1. 移至 https://cloud.redhat.com/openshift/install/azure/aro-provisioned。
1. 登入您的 Red Hat 帳戶，或使用您的公司電子郵件建立新的 Red Hat 帳戶;接受條款及條件。
1. 選取 [**下載提取密碼**]。

將*pull-secret*儲存在安全的地方;您會在每次建立叢集時使用檔案。

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
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> 建立叢集通常需要大約35分鐘的時間。

## <a name="access-the-cluster-console"></a>存取叢集主控台

您可以在 Azure Red Hat OpenShift 4.3 叢集資源下`https://console-openshift-console.apps.<random>.<location>.aroapp.io/`找到叢集主控台 URL （格式為）。 執行下列命令以查看資源：

```console
az aro list -o table
```

您可以使用`kubeadmin`使用者登入叢集。  執行下列命令來尋找`kubeadmin`使用者的密碼：

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
