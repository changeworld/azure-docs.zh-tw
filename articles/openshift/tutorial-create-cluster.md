---
title: 教學課程-建立 Azure Red Hat OpenShift 4 叢集
description: 了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 32069d9594d4579bd18ec3fd0e76af7bdc69f4d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232150"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>教學課程：建立 Azure Red Hat OpenShift 4 叢集

在本教學課程（共三部分）中，您將準備您的環境，以建立執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集，並建立叢集。 您將了解如何：
> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網
> * 部署叢集

## <a name="before-you-begin"></a>開始之前

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 版2.0.75 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

### <a name="install-the-az-aro-extension"></a>安裝`az aro`擴充功能
此`az aro`延伸模組可讓您直接從命令列使用 Azure CLI，來建立、存取和刪除 Azure Red Hat OpenShift 叢集。

執行下列命令來安裝`az aro`擴充功能。

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

如果您已安裝此延伸模組，您可以執行下列命令來進行更新。

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>註冊資源提供者

接下來，您必須在您`Microsoft.RedHatOpenShift`的訂用帳戶中註冊資源提供者。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

確認已註冊此延伸模組。

```azurecli-interactive
az -v
```

  您應該會取得如下所示的輸出。

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>取得 Red Hat pull 密碼（選擇性）

Red Hat 提取密碼可讓您的叢集存取 Red Hat 容器登錄，以及其他內容。 此步驟為選用步驟，但建議執行。

流覽至，然後按一下 [ https://cloud.redhat.com/openshift/install/azure/aro-provisioned *下載提取密碼*]，以取得您的提取密碼。

您必須登入 Red Hat 帳戶，或使用您的公司電子郵件建立新的 Red Hat 帳戶，並接受條款及條件。

將儲存`pull-secret.txt`的檔案保留在安全的地方-在每個建立叢集時都會用到它。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>建立包含兩個空白子網的虛擬網路

接下來，您將建立包含兩個空白子網的虛擬網路。

1. **設定下列變數。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **建立資源群組**

    Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create] [az-group-create] 命令來建立資源群組。

    ```azurecli-interactive
    az group create --name $CLUSTER --location $LOCATION
    ```

    下列範例輸出顯示已成功建立的資源群組：

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **建立虛擬網路。**

    執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集需要具有兩個空白子網的虛擬網路，適用于主要和背景工作角色節點。

    在您稍早建立的相同資源群組中建立新的虛擬網路。

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    下列範例輸出顯示已成功建立虛擬網路：

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **為主要節點新增空的子網。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **為背景工作節點新增空白子網。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **停用主要子網上的[子網私人端點原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。** 這是能夠連線和管理叢集的必要條件。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>建立叢集

執行下列命令來建立叢集。 （選擇性）您可以傳遞提取密碼，讓您的叢集能夠存取 Red Hat 容器登錄，以及其他內容。 流覽至[Red Hat OpenShift 叢集管理員](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)，然後按一下 [**複製提取密碼**]，以存取您的提取密碼。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> 建立叢集通常需要大約35分鐘的時間。

>[!IMPORTANT]
> 如果您選擇指定自訂網域（例如**foo.example.com**），OpenShift 主控台將會出現在之類的 URL `https://console-openshift-console.apps.foo.example.com`，而不是內建的網域。 `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> 根據預設，OpenShift 會針對在上`*.apps.<random>.<location>.aroapp.io`建立的所有路由使用自我簽署憑證。  如果您在連線至叢集之後選擇使用自訂 DNS，則必須遵循 OpenShift 檔，為您的輸入[控制器設定自訂 ca](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) ，並為[您的 API 伺服器設定自訂 ca](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)。
>

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網
> * 部署叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [連接到 Azure Red Hat OpenShift 叢集](tutorial-connect-cluster.md)
