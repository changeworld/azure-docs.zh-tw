---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
ms.topic: article
ms.date: 09/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: a36eb588c7128f13fb21b368d308ed00171fbb4b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335529"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 最近，Kubernetes 社區已將 [每個版本的支援視窗從9個月增加到12個月](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)，從1.19 版開始。 這些版本均包含新功能和增強功能。 修補程式版本更頻繁 (有時候是每週) ，而且適用于次要版本內的重大 bug 修正。 這些修補程式版本包括安全性弱點或主要 bug 的修正。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 會使用標準的 [語義版本](https://semver.org/) 控制版本配置，這表示每個版本的 Kubernetes 都會遵循此編號配置：

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

版本中的每個數位表示與舊版的一般相容性：

* 當不相容的 API 變更或回溯相容性可能中斷時，主要版本會變更。
* 當進行可回溯相容于其他次要版本的功能變更時，次要版本會變更。
* 當進行回溯相容錯誤修正時，修補程式版本會變更。

使用者應致力於執行其所執行次要版本的最新修補程式版本，例如，如果您的生產叢集已開啟， **`1.17.7`** 而且 **`1.17.8`** 是 *1.17* 系列可用的最新可用修補程式版本，您應該 **`1.17.8`** 儘快升級至，以確保您的叢集已完全修補並受到支援。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 會定義正式推出的版本，做為所有 SLO 或 SLA 測量中啟用的版本，以及適用于所有區域的版本。 AKS 支援三個 GA 次要版本的 Kubernetes：

* 在 AKS 中發行的最新 GA 次要版 (，我們會將其稱為 N) 。
* 兩個先前的次要版本。
* 每個支援的次要版本也最多可支援兩個 (2) 穩定的修補程式。
* AKS 可能也支援預覽版本，這些版本會明確標示並受限於 [預覽條款及條件][preview-terms]。

> [!NOTE]
> AKS 使用包含漸進式區域部署的安全部署實務。 這表示最多可能需要10個工作天的時間，新的發行或新版本才能在所有區域中使用。

AKS 上的 Kubernetes 版本支援的視窗稱為「N-2」： (N (最新版本) -2 (次要版本) # A5。

例如，如果 AKS 引進 *1.17* ，就會提供下列版本的支援：

新的次要版本    |    支援的版本清單
-----------------    |    ----------------------
1.17               |    1.17、1.17、1.16、1.16. d、1.15. e、1.15. f

其中 ". letter" 代表修補程式版本。

引進新的次要版本時，最舊的次要版本和所支援的修補程式版本會被取代並移除。 例如，如果目前支援的版本清單為：

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

和 AKS 版本 1.18 \* ，這表示所有1.15 都 \* 將移除，並在30天內不支援。

> [!NOTE]
> 請注意，如果客戶正在執行不受支援的 Kubernetes 版本，系統會要求他們在要求支援叢集時進行升級。 [AKS 支援原則](./support-policies.md)不包含執行不受支援之 Kubernetes 版本的叢集。

除了上述以外，AKS 最多可支援指定次要版本的兩個 **修補程式** 版本。 請提供下列支援的版本：

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

如果 AKS 發行 `1.17.9` 和 `1.16.11` ，最舊的修補程式版本會被取代和移除，而且支援的版本清單會變成：

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>支援的 `kubectl` 版本

您可以使用 `kubectl` 相對於 *kube-apiserver* 版本的較舊版本或較新版本，這與 [kubectl 的 Kubernetes 支援原則](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)一致。

例如，如果您的 *kube apiserver* 是在 *1.17*，則您可以使用 *1.16* 版的來 *1.18* 的 `kubectl` *kube apiserver*。

若要安裝或更新您的版本 `kubectl` ，請執行 `az aks install-cli` 。

## <a name="release-and-deprecation-process"></a>發行和淘汰程式

您可以參考 [AKS Kubernetes 發行行事曆](#aks-kubernetes-release-calendar)上即將發行的版本和棄用功能。

適用于 Kubernetes 的新 **次要** 版本
1. AKS 會發佈預先公告，其中包含新版本發行的計畫日期，以及在移除之前至少30天 [AKS 版本](https://aka.ms/aks/releasenotes) 資訊的原始版本淘汰。
2. AKS 會使用 AKS 和入口網站存取權，將 [服務健康狀態通知](../service-health/service-health-overview.md) 發佈給所有使用者，並傳送電子郵件給訂用帳戶管理員，並提供已規劃的版本移除日期。
3. 使用者有 **30 天** 的自移除版本，升級至支援的次要版本版本，以繼續接收支援。

適用于 Kubernetes 的新 **修補程式** 版本
  * 由於修補程式版本的緊急本質，因此可以在服務推出時將其引進。
  * 一般而言，AKS 並不會針對新修補程式版本的發行進行廣泛的通訊。 不過，AKS 會持續監視並驗證可用的 CVE 修補程式，以及時在 AKS 中支援這些修補程式。 如果發現重大修補程式或需要使用者動作，AKS 會通知使用者升級至新的可用修補程式。
  * 使用者有 **30 天** 的時間，從 AKS 中移除修補程式版本，升級為支援的修補程式並繼續接收支援。

### <a name="supported-versions-policy-exceptions"></a>支援的版本原則例外狀況

AKS 保留新增或移除新的/現有版本的許可權，這些版本已識別為有一或多個重大的生產影響 bug 或安全性問題，而不會事先通知。

您可以略過特定的修補程式版本，或根據 bug 或安全性問題的嚴重性來加速推出。

## <a name="azure-portal-and-cli-versions"></a>Azure 入口網站和 CLI 版本

當您在入口網站或 Azure CLI 部署 AKS 叢集時，叢集會預設為 N-1 次要版本和最新修補程式。 例如，如果 AKS 支援 *1.17. a*、 *1.17*、 *1.16*、 *1.16. d*、 *1.15*和 *1.15. f*，則選取的預設版本為 *1.16. c*。

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS** 區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes 發行行事曆

針對過去的版本歷程記錄，請參閱 [這裡](https://en.wikipedia.org/wiki/Kubernetes#History)。

|  K8s 版本 | 上游版本  | AKS 預覽  | AKS GA  | 生命週期結束 |
|--------------|-------------------|--------------|---------|-------------|
| 1.16  | Sep-19-19  | Jan 2019   | 三月2020  | 1.19 GA | 
| 1.17  | Dec-09-19  | Jan 2019   | 2020年7月  | 1.20 GA | 
| 1.18  | Mar-23-20  | 2020 年 5 月   | 2020年8月  | 1.21 GA | 
| 1.19  | 8月-04-20  | Sep 2020   | 11月2020  | 1.22 GA | 
| 1.20  | Dec-08-20  | Jan 2021   | 三月2021  | 1.23 GA | 

## <a name="faq"></a>常見問題集

**預期升級 Kubernetes 版本以保持支援的頻率為何？**

使用 Kubernetes 1.19 來說明， [開放原始碼社區已將支援擴展至1年](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)。 AKS 認可，以最少的時間啟用與上游承諾相符的修補程式和支援。 這表示從1.19 上的 AKS 叢集開始，您至少可以一年升級一次，以保持在支援的版本上。 若為1.18 或更低版本，支援的視窗會維持為9個月，每隔9個月需要升級一次，才能保持在支援的版本上。 強烈建議您定期測試新版本，並準備好升級至較新版本，以在 Kubernetes 內取得最新的穩定增強功能。

**當使用者使用不支援的次要版本升級 Kubernetes 叢集時，會發生什麼事？**

如果您是在 *3-3* 版或更舊版本，則表示您不在支援範圍內，因此將會要求您進行升級。 當您從第3版升級為 n-2 時，您會回到我們的支援原則中。 例如：

- 如果最舊的支援 AKS 版本是 *1.15* ，而且您是在 *1.14* 或更舊的版本上，則您不在支援範圍內。
- 從 *1.14* 升級至1.15 或更新版本成功時，您會回到我們 *的* 支援原則中。

不支援降級。

**「不支援」代表什麼意思**

「不支援」表示您正在執行的版本不在支援的版本清單中，而且在要求支援時，系統會要求您將叢集升級至支援的版本，除非您是在版本淘汰後30天的寬限期內。 此外，AKS 也不會對支援版本清單以外的叢集進行任何執行時間或其他保證。

**當使用者使用不支援的次要版本調整 Kubernetes 叢集時，會發生什麼事？**

針對 AKS 不支援的次要版本，相應縮小或相應放大應會繼續運作，但不會有服務保證的品質，因此強烈建議您升級以讓叢集重新進入支援。

**使用者是否可以永遠保持 Kubernetes 版本？**

如果叢集已不支援三個以上的 (3) 次要版本，且發現有安全性風險，Azure 會將您主動升級叢集。 如果您不採取進一步的動作，Azure 會保留您自動升級叢集的許可權。

**如果節點集區不在其中一個支援的 AKS 版本中，控制平面支援哪些版本？**

控制平面必須在所有節點集區的版本視窗內。 如需有關升級控制平面或節點集區的詳細資訊，請造訪 [升級節點](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)集區的相關檔。

**在叢集升級期間可以略過多個 AKS 版本嗎？**

當您升級支援的 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許在*1.12*2.x  ->  *.* x 或*1.13. x*  ->  *1.14*之間進行升級，但*1.12. x*  ->  *1.14*則否。

若要升級，請從*1.12.* x  ->  *1.14*，先從*1.12*2.x. x 升級  ->  * *，然後再從*1.13. x*  ->  *1.14*進行升級。

略過多個版本只能在從不支援的版本升級回支援的版本時進行。 例如，從不支援的 *1.10. x* 升級 > 支援的 *1.15。 x* 可以完成。

## <a name="next-steps"></a>後續步驟

如需如何升級叢集的相關資訊，請參閱[升級 Azure Kubernetes Service (AKS) 叢集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
