---
title: 將 Azure 開發人員空間群集配置為使用 Helm 3（預覽版）
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: 瞭解如何將開發人員空間群集配置為使用 Helm 3
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454290"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>將 Azure 開發人員空間群集配置為使用 Helm 3（預覽版）

預設情況下，Azure 開發空間使用 Helm 2 在 AKS 群集上的開發空間中安裝使用者服務。 您可以使 Azure 開發人員空間使用 Helm 3，而不是 Helm 2 在開發空間中安裝使用者服務。 無論 Helm Azure 開發人員空間用於安裝使用者服務的版本如何，您都可以繼續使用 Helm 2 或 3 用戶端管理同一群集上自己的版本。

啟用 Helm 3 時，Azure 開發人員空間在開發空間中以下列方式安裝使用者服務時的行為方式不同：

* 蒂勒不再部署在*azds*命名空間中的群集。
* Helm 在安裝服務的命名空間中存儲發佈資訊，而不是*azds*命名空間。
* Helm 3 發佈資訊將保留在刪除控制器後安裝服務的命名空間中。
* 您可以使用 Helm 3 用戶端直接與群集上的 Azure 開發人員空間管理的任何版本進行交互。

在本指南中，您將瞭解如何啟用 Azure 開發人員空間的 Helm 3，以便在開發空間中安裝使用者服務。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli]。

### <a name="register-the-helm3preview-preview-feature"></a>註冊 Helm3 預覽預覽功能

要使 Azure 開發人員空間使用 Helm 3 在開發空間中安裝使用者服務，請首先使用*az 功能寄存器*命令在訂閱上啟用*Helm3 預覽*功能標誌：

> [!WARNING]
> 使用*Helm3 預覽*功能標誌啟用 Azure 開發空間的任何 AKS 群集都將使用此預覽體驗。 要繼續在 AKS 群集上啟用完全支援的 Azure 開發空間，請不要在生產訂閱上啟用預覽功能。 使用單獨的測試或開發 Azure 訂閱來測試預覽功能。

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

完成註冊需要幾分鐘時間。 使用*az 要素顯示*命令檢查註冊狀態：

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

註冊*狀態**時，請*刷新*Microsoft.DevSpaces*使用*az 提供程式註冊*：

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>限制

此功能處於預覽版時，適用以下限制：

* 不能在具有現有工作負載的 AKS 群集上使用此功能。 您必須創建新的 AKS 群集。

## <a name="create-your-cluster"></a>建立叢集

在具有此預覽功能的區域創建新的 AKS 群集。 以下命令創建名為*MyResourceGroup*的資源組和名為*MyAKS*的新 AKS 群集：

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>啟用 Azure 開發空間

使用*使用-開發空間*命令在 AKS 群集上啟用開發空間，並按照提示操作。 下列命令會在 MyResourceGroup** 群組中的 MyAKS** 叢集上啟用 Dev Spaces，並建立「預設」開發空間。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>驗證開發空間正在運行頭盔 3

通過在*azds*命名空間中列出部署來驗證收銀台未運行：

```cmd
kubectl get deployment -n azds
```

確認在 azds 命名空間中未運行*耕種器部署*。 例如：

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md