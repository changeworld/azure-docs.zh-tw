---
title: 建立 Azure Red Hat OpenShift 4 私人叢集
description: 瞭解如何建立執行 OpenShift 4 的 Azure Red Hat OpenShift 私用叢集
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: cfc28577f089ef22457e9f66ff08106969a5a4b2
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857386"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>建立 Azure Red Hat OpenShift 4 私人叢集

在本文中，您將準備您的環境，以建立執行 OpenShift 4 的 Azure Red Hat OpenShift 私用叢集。 您將學習如何：

> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網
> * 部署具有私用 API 伺服器端點和私用輸入控制器的叢集

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 版2.0.75 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>開始之前

### <a name="install-the-az-aro-extension"></a>安裝 ' az aro ' 延伸模組
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

1. **[移至您的 Red Hat OpenShift cluster manager 入口網站](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)並登入。**

   您必須登入 Red Hat 帳戶，或使用您的公司電子郵件建立新的 Red Hat 帳戶，並接受條款及條件。

2. **按一下 [下載提取密碼]。**

將儲存`pull-secret.txt`的檔案保留在安全的地方-在每個建立叢集時都會用到它。

執行`az aro create`命令時，您可以使用參數來參考您的`--pull-secret @pull-secret.txt`提取密碼。 從`az aro create`您儲存`pull-secret.txt`盤案的目錄執行。 否則，請`@pull-secret.txt`將`@<path-to-my-pull-secret-file`取代為。

如果您要複製您的提取密碼，或在其他腳本中參考它，您的提取密碼應格式化為有效的 JSON 字串。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>建立包含兩個空白子網的虛擬網路

接下來，您將建立包含兩個空白子網的虛擬網路。

1. **設定下列變數。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **建立資源群組**

    Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create] [az-group-create] 命令來建立資源群組。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
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

執行下列命令來建立叢集。 （選擇性）您可以[傳遞 Red hat 提取密碼](#get-a-red-hat-pull-secret-optional)，讓您的叢集能夠存取 red hat 容器登錄，以及其他內容。

>[!NOTE]
> 如果您要複製/貼上命令，並使用其中一個選擇性參數，請務必刪除初始主題標籤和結尾的註解文字。 也請在命令的上一行關閉引數，並在結尾加上反斜線。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

執行`az aro create`命令之後，通常需要大約35分鐘的時間來建立叢集。

>[!IMPORTANT]
> 如果您選擇指定自訂網域（例如**foo.example.com**），OpenShift 主控台將會出現在之類的 URL `https://console-openshift-console.apps.foo.example.com`，而不是內建的網域。 `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> 根據預設，OpenShift 會針對在上`*.apps.<random>.<location>.aroapp.io`建立的所有路由使用自我簽署憑證。  如果您選擇自訂 DNS，連線到叢集之後，您必須遵循 OpenShift 檔，為您的輸入控制器和[自訂 ca 設定 API 伺服器](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)的[自訂 ca](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) 。

## <a name="connect-to-the-private-cluster"></a>連接到私人叢集

您可以使用`kubeadmin`使用者登入叢集。  執行下列命令來尋找`kubeadmin`使用者的密碼。

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

下列範例輸出顯示密碼將在中`kubeadminPassword`。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

您可以執行下列命令來尋找叢集主控台 URL，如下所示：`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> 若要連線到私用的 Azure Red Hat OpenShift 叢集，您必須從您建立的虛擬網路或在部署叢集的虛擬網路[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)的虛擬網路中，執行下列步驟。

在瀏覽器中啟動主控台 URL，並使用`kubeadmin`認證登入。

![Azure Red Hat OpenShift 登入畫面](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>安裝 OpenShift CLI

當您登入 OpenShift Web 主控台之後，請按一下 [ **？** ] 在右上方，然後按一下**命令列工具**。 下載適用于您電腦的版本。

![Azure Red Hat OpenShift 登入畫面](media/aro4-download-cli.png)

您也可以從<https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>下載適用于您電腦的最新 CLI 版本。

## <a name="connect-using-the-openshift-cli"></a>使用 OpenShift CLI 進行連線

取出 API 伺服器的位址。

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> 若要連線到私用的 Azure Red Hat OpenShift 叢集，您必須從您建立的虛擬網路或在部署叢集的虛擬網路[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)的虛擬網路中，執行下列步驟。

使用下列命令登入 OpenShift 叢集的 API 伺服器。 以您剛抓取的密碼取代** \<kubeadmin password>** 。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>後續步驟

在本文中，已部署執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集。 您已了解如何︰

> [!div class="checklist"]
> * 設定必要條件，並建立必要的虛擬網路和子網
> * 部署叢集
> * 使用`kubeadmin`使用者連接到叢集

前進到下一篇文章，以瞭解如何使用 Azure Active Directory 來設定叢集以進行驗證。


* [使用命令列設定 Azure Active Directory 的驗證](configure-azure-ad-cli.md)


* [使用 Azure 入口網站和 OpenShift web 主控台設定 Azure Active Directory 的驗證](configure-azure-ad-cli.md)
