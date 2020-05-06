---
title: 教學課程 - 連線至 Azure Red Hat OpenShift 4 叢集
description: 了解如何連線 Microsoft Azure Red Hat OpenShift 叢集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d7efe781f1ba2beb1fa7dd4fdaaad280fc789de2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204740"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>教學課程：連線至 Azure Red Hat OpenShift 4 叢集

在本教學課程 (三個部分中的第二部分) 中，您將透過 OpenShift Web 主控台，連線到以 kubeadmin 使用者身分執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集。 您會了解如何：
> [!div class="checklist"]
> * 取得叢集的 `kubeadmin` 認證
> * 安裝 OpenShift CLI
> * 使用 OpenShift CLI 連線至 Azure Red Hat OpenShift 叢集

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已建立 Azure Red Hat OpenShift 叢集。 如果您尚未完成這些步驟，而且想要跟著做，請從[教學課程 1 - 建立 Azure Red Hat OpenShift 4 叢集](tutorial-create-cluster.md)開始。

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.75 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="connect-to-the-cluster"></a>連線至叢集

您可以使用 `kubeadmin` 使用者來登入叢集。  執行以下命令來尋找 `kubeadmin` 使用者的密碼。

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

以下範例輸出顯示密碼將位於 `kubeadminPassword` 中。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

您可以執行以下命令來尋找叢集主控台 URL，如下所示：`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

在瀏覽器中啟動主控台 URL，並使用 `kubeadmin` 認證登入。

![Azure Red Hat OpenShift 登入畫面](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>安裝 OpenShift CLI

在您登入 OpenShift Web 主控台後，按一下右上方的 **？** ，然後按一下 [命令列工具]  。 下載適合您電腦的版本。

![Azure Red Hat OpenShift 登入畫面](media/aro4-download-cli.png)

您也可以從 <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> 下載適合您電腦的最新 CLI 版本。

如果您正在 Azure Cloud Shell 上執行命令，請下載適用於 Linux 的最新 OpenShift 4 CLI。

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>使用 OpenShift CLI 進行連線

擷取 API 伺服器的位址。

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

使用以下命令登入 OpenShift 叢集的 API 伺服器。 以您剛擷取的密碼取代 **\<kubeadmin 密碼>** 。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：
> [!div class="checklist"]
> * 取得叢集的 `kubeadmin` 認證
> * 安裝 OpenShift CLI
> * 使用 OpenShift CLI 連線至 Azure Red Hat OpenShift 叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [刪除 Azure Red Hat OpenShift 叢集](tutorial-delete-cluster.md)