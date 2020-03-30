---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593439"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包括對於安全性弱點或主要 Bug 的修正，這些安全性弱點或主要 Bug 會影響到根據 Kubernetes 在生產環境中執行的大量客戶和產品。

AKS 的目標是在上游版本發佈後的 30 天內認證和發佈新的 Kubernetes 版本，但需視版本的穩定性而定。

## <a name="kubernetes-versions"></a>庫伯內斯版本

庫伯內斯使用標準的[語義版本](https://semver.org/)控制版本版本方案。 這意味著 Kubernetes 的每個版本都遵循此編號方案：

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

版本中的每個數位都表示與以前版本的一般相容性：

* 當不相容的 API 更改或向後相容性可能中斷時，主要版本會發生變化。
* 當進行向後與其他次要版本相容的功能更改時，次要版本會更改。
* 進行向後相容的錯誤修復時，修補程式版本會更改。

使用者應旨在運行他們正在運行的次要版本的最新修補程式版本，例如，如果生產群集位於*1.12.14*和*1.12.15*上，則*1.12*系列可用的修補程式版本應升級至*1.12.15，* 只要能夠確保群集已完全修補和支援。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 支援函式庫伯奈的三個次要版本：

* AKS （N） 中發佈的當前次要版本
* 前兩個次要版本。 每個支援的次要版本也支援兩個穩定的修補程式。

這稱為"N-2"：（N（最新版本） - 2（次要版本）。

例如，如果 AKS 今天引入了*1.15.a，* 則為以下版本提供支援：

新的次要版本    |    受支援的版本清單
-----------------    |    ----------------------
1.15.a               |    1.15.a， 1.15.b， 1.14.c， 1.14.d， 1.13.e， 1.13.f

其中".letter"代表修補程式版本。

有關版本更改和期望的通信的詳細資訊，請參閱下面的"通信"。

引入新的次要版本時，將棄用並刪除支援的最舊的次要版本和修補程式版本。 例如，如果當前受支援的版本清單為：

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

AKS 版本 1.16。*，這意味著 1.13。* 版本（所有 1.13 版本）將被刪除，並且已表示支援。

> [!NOTE]
> 請注意，如果客戶正在運行不支援的 Kubernetes 版本，則在請求對群集的支援時，將要求他們進行升級。 運行不支援的庫伯奈斯版本的群集不在[AKS 支援策略](https://docs.microsoft.com/azure/aks/support-policies)中。

除了上述次要版本外，AKS 還支援給定次要版本的兩**個最新版本。** 例如，給定以下支援的版本：

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

如果上游 Kubernets 發佈了 1.15.3 和 1.14.6，並且 AKS 發佈了這些修補程式版本，則將棄用和刪除最舊的修補程式版本，並且支援的版本清單變為：

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>通訊

* 對於庫伯奈斯的新**小**版本
  * 將公開通知所有使用者新版本以及將刪除哪些版本。
  * 發佈新修補程式版本時，將同時刪除最舊的修補程式版本。
  * 客戶從公開通知日期起**有 30 天的時間**升級到受支援的次要版本版本。
* 對於庫伯奈斯的新**補丁**版本
  * 所有使用者都將收到新修補程式版本發佈的通知，並升級到最新的修補程式版本。
  * 在刪除最舊的修補程式版本之前，使用者有**30 天的時間**升級到較新的受支援的修補程式版本。

AKS 將"發佈版本"定義為一般可用版本，在所有 SLO / 服務品質測量中啟用，並且在所有區域都可用。 AKS 還可能支援預覽版，這些版本已明確標記，並受預覽條款及條件的約束。

#### <a name="notification-channels-for-aks-changes"></a>AKS 更改的通知通道

AKS 發佈定期服務更新，其中匯總了[在 GitHub](https://github.com/Azure/AKS/releases)上發佈的新 Kubernetes 版本、服務更改和元件更新。

這些更改作為託管服務的一部分的常規維護的一部分滾動到所有客戶，有些需要顯式升級，而其他更改則不需要任何操作。

通知也通過：

* [AKS 版本資訊](https://aka.ms/aks/releasenotes)
* Azure 入口網站通知
* [Azure 更新通道][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>受支援的版本原則例外

AKS 保留添加或刪除已標識具有一個或多個影響 Bug 或安全問題的關鍵生產版本的權利，恕不另行通知。

可能會跳過特定的修補程式版本，或者根據 Bug 或安全問題的嚴重性加速推出。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 門戶和 CLI 預設版本

在門戶中或使用 Azure CLI 部署 AKS 群集時，該群集預設為 N-1 次要版本和最新修補程式。 例如，如果 AKS 支援*1.15.a* *、1.15.b* *、1.14.c* *、1.14.d、1.13.e*和*1.13.f，* 則所選預設版本為*1.14.c*。 *1.13.e*

AKS 選擇預設的 N-1，以預設為客戶提供已知、穩定和修補的版本。

## <a name="list-currently-supported-versions"></a>列出目前支援的版本

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS** 區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>常見問題集

**當客戶使用不支援的次要版本升級 Kubernetes 叢集時，會發生什麼事？**

如果您處於*n-3*版本，則表示您不在支援範圍之外，系統將要求您進行升級。 如果您從 n-3 版本升級到 n-2 成功，您現在在我們的支援政策範圍內。 例如：

- 如果受支援的最古老的 AKS 版本是*1.13.a，* 而您位於*1.12.b*或更舊版本上，則您不在支援範圍之外。
- 如果從*1.12.b*升級到*1.13.a*或更高版本成功，您將回到我們的支援政策中。

不支援升級到早于支援*N-2*視窗的版本。 在這種情況下，我們建議客戶創建新的 AKS 群集，並在受支援的視窗中使用版本重新部署其工作負載。

**"支援之外"是什麼意思**

"支援之外"表示正在運行的版本不在支援的版本清單之外，在請求支援時，系統將要求您將群集升級到受支援的版本。 此外，AKS 不會為受支援的版本清單之外的群集進行任何運行時或其他保證。

**當客戶使用不支援的次要版本調整 Kubernetes 叢集時，會發生什麼事？**

對於 AKS 不支援的次要版本，擴展或擴展應繼續工作，但強烈建議升級以恢復群集的支援。

**客戶可以永遠停留在某個 Kubernetes 版本嗎？**

是。 但是，如果群集不在 AKS 支援的版本之一上，則群集不在 AKS 支援策略中。 Azure 不會自動升級您的叢集或刪除它。

**如果節點池不在支援的 AKS 版本之一中，控制平面支援什麼版本？**

控制平面必須在所有節點池的版本視窗中。 有關升級控制平面或節點池的詳細資訊，請訪問有關[升級節點池](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)的文檔。

## <a name="next-steps"></a>後續步驟

如需如何升級叢集的相關資訊，請參閱[升級 Azure Kubernetes Service (AKS) 叢集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
