---
title: 將您的 Azure Dev Spaces 叢集設定為使用 Helm 3 （預覽）
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: 瞭解如何設定您的 Dev Spaces 叢集以使用 Helm 3
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202256"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>將您的 Azure Dev Spaces 叢集設定為使用 Helm 3 （預覽）

Azure Dev Spaces 預設會使用 Helm 2，在您的 AKS 叢集上安裝 Dev Spaces 中的使用者服務。 您可以讓 Azure Dev Spaces 使用 Helm 3，而不是 Helm 2 在 Dev Spaces 中安裝使用者服務。 無論 Helm Azure Dev Spaces 用來安裝使用者服務的版本為何，您都可以繼續使用 Helm 2 或3用戶端，在相同的叢集上管理您自己的版本。

當您啟用 Helm 3 時，Azure Dev Spaces 在以下列方式在 Dev Spaces 中安裝使用者服務時，會以不同的方式運作：

* Tiller 不再部署至*azds*命名空間中的叢集。
* Helm 會將發行資訊儲存在服務安裝所在的命名空間中，而不是*azds*命名空間。
* Helm 3 版本資訊會保留在刪除控制器之後安裝服務的命名空間中。
* 您可以使用 Helm 3 用戶端，直接與叢集上 Azure Dev Spaces 所管理的任何發行進行互動。

在本指南中，您將瞭解如何啟用 Helm 3，讓 Azure Dev Spaces 在 Dev Spaces 中安裝使用者服務。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI][azure-cli]。

### <a name="register-the-helm3preview-preview-feature"></a>註冊 Helm3Preview 預覽功能

若要讓 Azure Dev Spaces 使用 Helm 3 在 Dev Spaces 中安裝使用者服務，請先使用*az feature register*命令，在您的訂用帳戶上啟用*Helm3Preview*功能旗標：

> [!WARNING]
> 任何您使用*Helm3Preview*功能旗標 Azure Dev Spaces 開啟的 AKS 叢集都會使用此預覽體驗。 若要繼續在 AKS 叢集上啟用完全支援的 Azure Dev Spaces，請勿在生產訂用帳戶上啟用預覽功能。 使用個別的測試或開發 Azure 訂用帳戶來測試預覽功能。

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

註冊需要幾分鐘的時間才能完成。 使用*az feature show*命令來檢查註冊狀態：

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

當*狀態*為 [已*註冊*] 時，請使用*Az provider register*重新整理*DevSpaces*的註冊：

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>限制

這項功能處於預覽狀態時，適用下列限制：

* 您無法在具有現有工作負載的 AKS 叢集上使用這項功能。 您必須建立新的 AKS 叢集。

## <a name="create-your-cluster"></a>建立叢集

在具有這項預覽功能的區域中建立新的 AKS 叢集。 下列命令會在*美國中南部*區域建立名為*MyResourceGroup*的資源群組和名為*MyAKS*的新 AKS 叢集：

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>啟用 Azure Dev Spaces

使用 [*使用 dev spaces* ] 命令，在 AKS 叢集上啟用 dev spaces，並遵循提示。 下列命令會在*MyResourceGroup*群組中的*MyAKS*叢集上啟用 Dev Spaces，並建立預設的開發人員空間。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>確認 Dev Spaces 正在執行 Helm 3

在*azds*命名空間中列出部署，確認 tiller 並未執行：

```cmd
kubectl get deployment -n azds
```

確認*tiller-部署*並未在 azds 命名空間中執行。 例如，

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