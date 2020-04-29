---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593439"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包括對於安全性弱點或主要 Bug 的修正，這些安全性弱點或主要 Bug 會影響到根據 Kubernetes 在生產環境中執行的大量客戶和產品。

AKS 的目標是在上游發行的30天內認證併發行新的 Kubernetes 版本，受限於版本的穩定性。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用標準的[語義版本](https://semver.org/)控制版本設定配置。 這表示每個版本的 Kubernetes 都會遵循此編號配置：

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

版本中的每個數位都表示與先前版本的一般相容性：

* 當不相容的 API 變更或回溯相容性可能中斷時，主要版本會變更。
* 次要版本會在對其他次要版本回溯相容的功能變更時變更。
* 當發生回溯相容的 bug 修正時，修補程式版本會變更。

使用者應該將其目標設為執行最新的修補程式版本，其為其所執行的次要版本，例如，如果您的生產叢集在*1.12.14* ，而*1.12.15*是*1.12*系列可用的最新可用修補程式版本，則您應該儘快升級為*1.12.15* ，以確保您的叢集已完全修補並受到支援。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 支援三個次要版本的 Kubernetes：

* 在 AKS （N）中發行的目前次要版本
* 兩個先前的次要版本。 每個支援的次要版本也支援兩個穩定的修補程式。

這就是所謂的「N-2」：（N （最新版本）-2 （次要版本））。

例如，如果 AKS 引進了*1.15* ，就會提供下列版本的支援：

新的次要版本    |    支援的版本清單
-----------------    |    ----------------------
1.15。               |    1.15. a、1.15、1.14、1.14. d、1.13. e、1.13. f

其中 ". 字母" 代表修補程式版本。

如需有關版本變更和預期的通訊詳細資料，請參閱下面的「通訊」。

引進新的次要版本時，最舊的次要版本和支援的修補程式版本會被取代並移除。 例如，如果目前支援的版本清單為：

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

和 AKS 版本1.16。*，這表示1.13。* 版本（所有1.13 版）將會移除且不支援。

> [!NOTE]
> 請注意，如果客戶執行不受支援的 Kubernetes 版本，系統會要求他們在要求叢集的支援時進行升級。 [AKS 支援原則](https://docs.microsoft.com/azure/aks/support-policies)不會涵蓋執行不受支援 Kubernetes 版本的叢集。

除了上述次要版本以外，AKS 還支援給定次要版本的兩個最新**修補程式**版本。 例如，假設有下列支援的版本：

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

如果上游 Kubernetes 已發行1.15.3，而1.14.6 和 AKS 發行了這些修補程式版本，則最舊的修補程式版本會被取代並移除，而且支援的版本清單會變成：

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>通訊

* 適用于 Kubernetes 的新**次要**版本
  * 所有使用者都會在新版本中公開通知，而將移除哪個版本。
  * 發行新的修補程式版本時，會同時移除最舊的修補程式發行。
  * 客戶從公開通知日期起算**30 天**，以升級至支援的次要版本發行。
* 針對 Kubernetes 的新**修補程式**版本
  * 系統會通知所有使用者發行的新修補程式版本，並升級至最新的修補程式版本。
  * 使用者有**30 天**的時間，可以在移除最舊的更新版本之前，先升級至較新的支援修補程式。

AKS 會將「已發行版本本」定義為正式推出的版本，並在所有 SLO/服務品質測量中啟用，並可在所有區域使用。 AKS 也可以支援已明確加上標籤並受限於預覽條款及條件的預覽版本。

#### <a name="notification-channels-for-aks-changes"></a>AKS 變更的通知通道

AKS 會發佈週期性服務更新，其摘要說明[GitHub](https://github.com/Azure/AKS/releases)上的服務已發行的新 Kubernetes 版本、服務變更和元件更新。

這些變更會匯總給所有客戶，做為受管理服務所提供的定期維護的一部分，有些則需要明確升級，而有些則不需要採取任何動作。

通知也會透過下列方式傳送：

* [AKS 版本資訊](https://aka.ms/aks/releasenotes)
* Azure 入口網站通知
* [Azure 更新通道][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>支援的版本原則例外狀況

AKS 保留新增或移除已識別為有一或多個重大生產影響 bug 或安全性問題的權利，而不會事先通知。

視錯誤或安全性問題的嚴重性而定，可能會略過特定的修補程式版本或首度發行加速。

### <a name="azure-portal-and-cli-default-versions"></a>Azure 入口網站和 CLI 預設版本

當您在入口網站或 Azure CLI 中部署 AKS 叢集時，叢集會預設為 N-1 次要版本和最新的修補程式。 例如，如果 AKS 支援*1.15. a*、 *1.15*、 *1.14 .c*、 *1.14. d*、 *1.13. e*和*1.13. f*，則選取的預設版本為*1.14. c*。

AKS 會選擇預設的 N-1，以預設為客戶提供已知、穩定且已修補的版本。

## <a name="list-currently-supported-versions"></a>列出目前支援的版本

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS** 區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>常見問題集

**當客戶使用不支援的次要版本升級 Kubernetes 叢集時，會發生什麼事？**

如果您的版本是*n-3* ，則不受支援，將會要求您進行升級。 如果您從版本 n-3 升級至 n-2 成功，則您現在已在我們的支援原則中。 例如：

- 如果最舊的支援 AKS 版本為*1.13. a* ，而您在*1.12. b*或更舊版本中，則不支援。
- 如果從*1.12. b*升級至*1.13.* 或更新版本成功，您就會回到我們的支援原則中。

不支援升級至比支援的*N-2*視窗更舊的版本。 在這種情況下，我們建議客戶建立新的 AKS 叢集，並在支援的視窗中使用版本重新部署其工作負載。

**「在支援外」的平均值**

「不支援」表示您正在執行的版本不在支援的版本清單中，而且在要求支援時，系統會要求您將叢集升級為支援的版本。 此外，AKS 不會對支援的版本清單以外的叢集進行任何執行時間或其他保證。

**當客戶使用不支援的次要版本調整 Kubernetes 叢集時，會發生什麼事？**

針對 AKS 不支援的次要版本，相應縮小或相應放大應該會繼續正常執行，但強烈建議您升級，讓您的叢集回到支援。

**客戶可以永遠停留在某個 Kubernetes 版本嗎？**

是。 不過，如果叢集不在 AKS 所支援的其中一個版本上，叢集就會超出 AKS 支援原則。 Azure 不會自動升級您的叢集或刪除它。

**如果節點集區不在其中一個支援的 AKS 版本中，控制平面支援哪些版本？**

控制平面必須位於所有節點集區的版本視窗內。 如需有關升級控制平面或節點集區的詳細資訊，請造訪[升級節點](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)集區的相關檔。

## <a name="next-steps"></a>後續步驟

如需如何升級叢集的相關資訊，請參閱[升級 Azure Kubernetes Service (AKS) 叢集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
