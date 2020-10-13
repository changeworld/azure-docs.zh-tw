---
title: 商務持續性和災害復原
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: 瞭解如何使用 Azure Dev Spaces 和 Azure Kubernetes Services 來供應商務持續性，並為嚴重損壞修復做好準備
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 824eb1e65e7da5736080e47509aa163a868d8ec9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973132"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Azure Dev Space 的商務持續性和災害復原

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>檢閱 Azure Kubernetes Service (AKS) 的災害復原指引

Azure Dev Spaces 是 Azure Kubernetes Service (AKS) 的功能之一。 您應留意 AKS 災害復原的指導方針，並考量那是否適用於您在 Dev Spaces 中使用的 AKS 叢集。 如需詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中的商務持續性和災害復原的最佳做法](../../aks/operator-best-practices-multi-region.md)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>在不同區域的 AKS 叢集上啟用 Dev Spaces

在不同區域的 AKS 叢集上啟用 Dev Spaces，可讓您在某個 Azure 區域失敗後能立即繼續使用 Dev Spaces。

如需 AKS 多重區域部署的詳細資訊，請參閱[多重區域部署規劃](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-cli"></a>透過 Azure CLI 啟用 Dev Spaces

您也可以在命令列上啟用 Dev Spaces：

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>將小組的基準部署到每個叢集

在使用 Dev Spaces 時，您通常會將整個應用程式部署到 Kubernetes 叢集上的父開發人員空間。 依預設會使用 `default` 空間。 在初始部署會包含所有服務，以及這些服務所依賴的外部資源，例如資料庫或佇列。 這就是所謂的*基準*。 在父開發人員空間中設定基準之後，您就可以在子開發人員空間內逐一查看並偵錯個別的服務。

您應將最新版本的服務基準集部署到多個區域中的叢集。 以這種方式更新您的基準服務，可確保您在遇到 Azure 區域失敗的狀況時將可繼續使用 Dev Spaces。 例如，如果您透過 CI/CD 管線部署基準，請修改管線，使其部署到不同區域的多個叢集。

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>選取要用於 Dev Spaces 的正確 AKS 叢集

在正確設定執行小組基準的備份叢集後，您即可隨時快速切換至備份叢集。 然後，您可以在子開發人員空間中重新執行您正在處理的個別服務。

請使用下列 CLI 命令來選取不同的叢集：

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

您可以使用下列命令，列出可在新叢集上使用的開發人員空間：

```cmd
azds space list
```

您可以建立新的開發人員空間以供使用，或使用下列命令選取現有的開發人員空間：

```cmd
azds space select -n <space name>
```

執行這些命令之後，選取的叢集和開發人員空間將用於後續的 CLI 作業，以及對使用 Azure Dev Spaces 的 Visual Studio Code 擴充功能的專案進行偵錯。

如果您使用 Visual Studio，您可以透過下列步驟來切換現有專案所使用的叢集：

1. 在 Visual Studio 中，開啟您的專案。
1. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [屬性]****
1. 在左側窗格中按一下 [偵錯]****
1. 在 [偵錯屬性] 頁面上按一下 [設定檔]**** 下拉式清單，然後選擇 [Azure Dev Spaces]****。
1. 按一下 **[變更]** 按鈕。
1. 在顯示的對話方塊中，選取您要使用的 AAS 叢集。 如有需要，請選擇不同的開發人員空間來使用，從 [空間]**** 下拉式清單中選取適當的選項，以建立新的開發人員空間。

選取正確的叢集和空間後，您可以按 F5 在 Dev Spaces 中執行服務。

對任何設定為要使用原始叢集的其他專案重複這些步驟。

## <a name="access-a-service-on-a-backup-cluster"></a>存取備份叢集上的服務

如果您已將服務設定為使用公用 DNS 名稱，則在備份叢集上執行服務時，服務將會有不同的 URL。 公用 DNS 名稱的格式一律為 `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`。 如果您切換至不同的叢集，叢集 GUID 將會變更，而區域也有此可能。

執行 `azds up` 時，或是在 Visual Studio **Azure Dev Spaces** 下的 [輸出] 視窗中，Dev Spaces 一律會顯示正確的服務 URL。

您也可以執行 `azds list-uris` 命令以尋找 URL：
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

存取服務時請使用此 URL。
