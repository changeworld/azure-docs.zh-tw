---
title: 教學課程 - 建立 Azure Red Hat OpenShift 4 叢集
description: 了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: a581678fdd05dade336f7ca9fcbcf5ad4c92d49a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300165"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>教學課程：建立 Azure Red Hat OpenShift 4 叢集

在本教學課程 (三部分中的第一部分) 中，您將準備環境來建立執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集，並建立叢集。 您將了解如何：
> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網路
> * 部署叢集

## <a name="before-you-begin"></a>開始之前

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

Azure Red Hat OpenShift 至少需要 40 個核心，才能建立和執行 OpenShift 叢集。 新 Azure 訂用帳戶的預設 Azure 資源配額不符合這項需求。 若要要求增加資源限制，請參閱[標準配額：VM 系列的增加限制](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。

### <a name="verify-your-permissions"></a>驗證權限

若要建立 Azure Red Hat OpenShift 叢集，請在您的 Azure 訂用帳戶、Azure Active Directory 使用者或服務主體上確認下列權限：

|權限|包含 VNet 的資源群組|使用者執行 `az aro create`|服務主體作為 `–client-id` 傳遞|
|----|:----:|:----:|:----:|
|**使用者存取系統管理員**|X|X| |
|**參與者**|X|X|X|

### <a name="register-the-resource-provider"></a>註冊資源提供者

接下來，您必須在您的訂用帳戶中註冊 `Microsoft.RedHatOpenShift` 資源提供者。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>取得 Red Hat 提取祕密 (選擇性)

Red Hat 提取祕密可讓您的叢集存取 Red Hat 容器登錄及其他內容。 此步驟為選用步驟，但建議執行。

1. **[瀏覽至您的 Red Hat OpenShift 叢集管理員入口網站](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) 並登入。**

   您必須登入 Red Hat 帳戶，或使用您的公司電子郵件建立新的 Red Hat 帳戶，並接受條款及條件。

2. 如果這是您第一次建立叢集，請移至 [**OpenShift 產品頁面**](https://developers.redhat.com/products/codeready-containers)。 註冊之後，請前往 [ **[Red Hat OpenShift 叢集管理員] 頁面**](https://cloud.redhat.com/openshift/)，在其中按一下 [下載提取祕密] 並下載要與 ARO 叢集搭配使用的提取祕密。

將儲存的 `pull-secret.txt` 檔案保持在其他安全位置。 如果您需要建立叢集，且叢集內包含 Red Hat 或認證合作夥伴的範例或操作員，則會在每次建立叢集時使用此檔案。

執行 `az aro create` 命令時，您可以使用 `--pull-secret @pull-secret.txt` 參數來參考您的提取祕密。 從您儲存 `pull-secret.txt` 檔案的目錄執行 `az aro create`。 否則，請將 `@pull-secret.txt` 取代為 `@<path-to-my-pull-secret-file>`。

如果您要複製提取祕密，或在其他指令碼中參考，您的提取祕密應格式化為有效的 JSON 字串。

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>為叢集準備自訂網域 (選擇性)

執行 `az aro create` 命令時，您可以使用 `--domain foo.example.com` 參數來指定叢集的自訂網域。

如果您為叢集提供自訂網域，請注意下列幾點：

* 建立叢集之後，您必須在 DNS 伺服器中針對指定的 `--domain` 建立 2 個 DNS A 記錄：
    * **api** - 指向 API 伺服器
    * **\*.apps** - 指向輸入
    * 藉由執行下列命令來取得這些值：`az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'`。

* OpenShift 主控台將會在 URL (例如 `https://console-openshift-console.apps.foo.example.com`) 中提供，而不是內建的網域 `https://console-openshift-console.apps.<random>.<location>.aroapp.io`。

* 根據預設，OpenShift 會針對 `*.apps.<random>.<location>.aroapp.io` 上建立的所有路由使用自我簽署憑證。  如果您在連線至叢集之後選擇使用自訂 DNS，則必須遵循 OpenShift 文件來[為您的輸入控制器設定自訂 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html)，以及[為您的 API 伺服器自訂 CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>建立包含兩個空白子網路的虛擬網路

接下來，您將建立包含兩個空白子網路的虛擬網路。

1. **在您將執行 `az` 命令的殼層環境中，設定下列變數。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **建立資源群組。**

    Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 命令來建立資源群組。
    
> [!NOTE]
> Azure Red Hat OpenShift 無法在可建立 Azure 資源群組的所有區域中使用。 如需支援 Azure Red Hat OpenShift 區域的相關資訊，請參閱[可用區域](https://docs.openshift.com/aro/4/welcome/index.html#available-regions)。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    The following example output shows the resource group created successfully:

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

    執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集需要具有兩個空白子網路的虛擬網路，分別用於主要節點和背景工作角色節點。

    在您稍早建立的相同資源群組中建立新的虛擬網路：

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    下列範例輸出顯示已成功建立的虛擬網路：

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

3. **為主要節點新增空白子網路。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **為背景工作節點新增空白子網路。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[停用主要子網上的子網路私人端點原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。** 這是能夠連線和管理叢集的必要條件。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>建立叢集

執行下列命令來建立叢集： 如果您選擇使用下列其中一個選項，請據以修改命令：
* (選用) 您可以[傳遞 Red Hat 提取祕密](#get-a-red-hat-pull-secret-optional)，讓您的叢集能夠存取 Red Hat 容器登錄與其他內容。 將 `--pull-secret @pull-secret.txt` 引數新增至您的命令。
* (選擇性) 您可以[使用自訂網域](#prepare-a-custom-domain-for-your-cluster-optional)。 將 `--domain foo.example.com` 引數新增至您的命令，並將 `foo.example.com` 取代為您自己的自訂網域。

> [!NOTE]
> 如果您要將任何選擇性引數新增至您的命令，請務必在命令的前一行結尾加上反斜線以關閉引數。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

執行 `az aro create` 命令之後，一般大約需要 35 分鐘的時間來建立叢集。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網路
> * 部署叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [連線至 Azure Red Hat OpenShift 叢集](tutorial-connect-cluster.md)
