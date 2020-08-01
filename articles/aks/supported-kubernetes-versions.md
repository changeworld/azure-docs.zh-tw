---
title: Azure Kubernetes Service 中支援的 Kubernetes 版本
description: 了解 Azure Kubernetes Service (AKS) 中叢集的 Kubernetes 版本支援原則和生命週期
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 273c41a523de8b2776982e5229c5a8b618b82c19
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475187"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中支援的 Kubernetes 版本

Kubernetes 社群大約每隔三個月就會發行次要版本。 這些版本均包含新功能和增強功能。 修補程式版本會更頻繁地發行 (有時每週)，而且僅適用於次要版本中的重要 Bug 修正。 這些修補程式版本包含安全性弱點或主要 bug 的修正程式。

## <a name="kubernetes-versions"></a>Kubernetes 版本

Kubernetes 使用標準的[語義版本](https://semver.org/)控制版本控制配置，這表示每個 Kubernetes 版本都遵循此編號配置：

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

版本中的每個數位都表示與先前版本的一般相容性：

* 當不相容的 API 變更或回溯相容性可能中斷時，主要版本會變更。
* 次要版本會在對其他次要版本回溯相容的功能變更時變更。
* 當發生回溯相容的 bug 修正時，修補程式版本會變更。

使用者應該將其目標設為執行次要版本的最新修補程式版本，例如，如果您的生產叢集是 on， **`1.17.7`** 而且 **`1.17.8`** 是*1.17*系列可用的最新可用修補程式版本，您應該 **`1.17.8`** 儘快升級至，以確保您的叢集受到完整的修補和支援。

## <a name="kubernetes-version-support-policy"></a>Kubernetes 版本支援原則

AKS 會定義正式推出的版本，做為所有 SLO 或 SLA 測量中已啟用的版本，以及所有區域中的可用時間。 AKS 支援三個 GA 次要版本的 Kubernetes：

* 在 AKS 中發行的最新 GA 次要版本（我們將稱之為 N）。 
* 兩個先前的次要版本。 
* 每個支援的次要版本也支援最多兩個（2）穩定的修補程式。
* AKS 也可以支援預覽版本，其已明確加上標籤，並受限於[預覽條款及條件][preview-terms]。

> [!NOTE]
> AKS 會使用包含漸進式區域部署的安全部署實務。 這表示最多可能需要10個工作天，新版本或新版本才能在所有區域中使用。

AKS 上的 Kubernetes 版本支援的視窗稱為「N-2」：（N （最新版本）-2 （次要版本））。

例如，如果 AKS 引進了*1.17* ，就會提供下列版本的支援：

新的次要版本    |    支援的版本清單
-----------------    |    ----------------------
1.17。               |    1.17、1.17、1.16、1.16、1.15. e、1.15. f

其中 ". 字母" 代表修補程式版本。

引進新的次要版本時，最舊的次要版本和支援的修補程式版本會被取代並移除。 例如，如果目前支援的版本清單為：

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

和 AKS 發行 1.18 \* ，這表示所有 1.15. \* 版本將會移除，且在30天內將不會受到支援。

> [!NOTE]
> 請注意，如果客戶執行不受支援的 Kubernetes 版本，系統會要求他們在要求叢集的支援時進行升級。 [AKS 支援原則](./support-policies.md)不會涵蓋執行不受支援 Kubernetes 版本的叢集。

除了上述以外，AKS 最多可支援給定次要版本的兩個**修補程式**版本。 因此，請提供下列支援的版本：

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

如果 AKS 發行 `1.17.9` 和 `1.16.11` ，最舊的修補程式版本會被取代並移除，而且支援的版本清單會變成：

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>支援的 `kubectl` 版本

您可以使用比 kube-kube-apiserver 版本更舊或較新的次要版本 `kubectl` ，這與[Kubectl 的 Kubernetes 支援原則](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)一致。 *kube-apiserver*

例如，如果您的*kube kube-apiserver*位於*1.17*，則您可以使用*1.16*版的來*1.18* `kubectl` 與該*kube-kube-apiserver*。

若要安裝或更新您的版本 `kubectl` ，請執行 `az aks install-cli` 。

## <a name="release-and-deprecation-process"></a>發行和淘汰程式

您可以參考[AKS Kubernetes 發行日曆](#aks-kubernetes-release-calendar)上即將發行的版本和棄用功能。

適用于 Kubernetes 的新**次要**版本
1. AKS 會發佈預先公告，其中包含新版本發行的計畫日期，以及在[AKS 版本](https://aka.ms/aks/releasenotes)資訊中，移除前至少30天的舊版本。
2. AKS 會將[服務健康狀態通知](../service-health/service-health-overview.md)發佈給所有具有 AKS 和入口網站存取權的使用者，並將電子郵件傳送給訂用帳戶管理員，並提供已規劃的版本移除日期。
3. 使用者已從版本移除**30 天**，升級至支援的次要版本，以繼續接收支援。

針對 Kubernetes 的新**修補程式**版本
  * 由於修補程式版本的緊急本質，因此可以在服務可供使用時將其引進。
  * 一般來說，AKS 並不會針對新的修補程式版本，進行廣泛的通訊。 不過，AKS 會持續監視並驗證可用的 CVE 修補程式，以及時在 AKS 中支援它們。 如果找到重要的修補程式或需要使用者動作，AKS 會通知使用者升級至新的可用修補程式。
  * 從 AKS 中移除修補程式版本後的**30 天內**，使用者會升級為支援的修補程式，並繼續接收支援。

### <a name="supported-versions-policy-exceptions"></a>支援的版本原則例外狀況

AKS 保留新增或移除已識別為有一或多個重大生產影響 bug 或安全性問題的權利，而不會事先通知。

視錯誤或安全性問題的嚴重性而定，可能會略過特定的修補程式版本或首度發行加速。

## <a name="azure-portal-and-cli-versions"></a>Azure 入口網站和 CLI 版本

當您在入口網站或 Azure CLI 中部署 AKS 叢集時，叢集會預設為 N-1 次要版本和最新的修補程式。 例如，如果 AKS 支援*1.17*、 *1.17*、 *1.16*、 *1.16*、1.15 *. e*和*1.15. f*，則選取的預設版本為*1.16. c*。

若要找出目前可供您訂用帳戶和區域使用的版本，請使用 [az aks get-versions][az-aks-get-versions] 命令。 下列範例會列出 EastUS** 區域的可用 Kubernetes 版本：

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes 發行行事曆

如需過去的發行歷程記錄，請參閱[這裡](https://en.wikipedia.org/wiki/Kubernetes#History)。

|  K8s 版本 | 上游版本  | AKS 預覽  | AKS GA  | 生命週期結束 |
|--------------|-------------------|--------------|---------|-------------|
| 1.17  | Dec-09-19  | Jan 19   | 7月20日  | 1.20 GA | 
| 1.18  | Mar-23-20  | 5月20   | 8月20日  | 1.21 GA | 
| 1.19  | 8月-04-20  | 8月20日   | 11 月 20 日  | 1.22 GA | 
| 1.20  | * 11 月20日    | * Dec 20   | * Jan 21  | 1.23 GA | 

\*暫止上游發行日期確認。

## <a name="faq"></a>常見問題集

**當使用者升級的 Kubernetes 叢集具有不支援的次要版本時，會發生什麼事？**

如果您是在*3*個或更舊版本，則表示您不在支援範圍內，而且會要求您進行升級。 當您從第 n-3 版升級至 n-2 成功時，您就會回到我們的支援原則中。 例如：

- 如果最舊的支援 AKS 版本是*1.15* ，而且您是在*1.14*或更舊版本上，您就不會在支援範圍內。
- 當從*1.14*升級到*1.15*時，或更新版本成功時，您就會回到我們的支援原則中。

不支援降級。

**「在支援外」的平均值**

「不支援」表示您正在執行的版本不在支援的版本清單中，而且在要求支援時，系統會要求您將叢集升級為支援的版本，除非您在淘汰版本後的30天寬限期內。 此外，AKS 不會對支援的版本清單以外的叢集進行任何執行時間或其他保證。

**當使用者使用不受支援的次要版本來調整 Kubernetes 叢集時，會發生什麼事？**

針對 AKS 不支援的次要版本，相應縮小或相應放大應該會繼續執行，但不會有任何服務保證品質，因此強烈建議您升級，讓您的叢集回到支援。

**使用者可以永遠保持 Kubernetes 版本嗎？**

如果叢集已不支援三（3）個次要版本，而且發現有安全性風險，Azure 會聯絡您主動升級叢集。 如果您未採取進一步的動作，Azure 會保留自動代表您升級叢集的權利。

**如果節點集區不在其中一個支援的 AKS 版本中，控制平面支援哪些版本？**

控制平面必須位於所有節點集區的版本視窗內。 如需有關升級控制平面或節點集區的詳細資訊，請造訪[升級節點](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)集區的相關檔。

**在叢集升級期間可以略過多個 AKS 版本嗎？**

當您升級支援的 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許在*1.12.* x  ->  *1.13. x*或*1.13. x*  ->  *1.14*之間進行升級，但*1.12. x*  ->  *1.14*不是。

若要升級，請從*1.12. x*  ->  *1.14*，先從*1.12.* x 1.13. x 升級  ->  * *，然後從*1.13. x*  ->  *1.14*升級。

只有在從不受支援的版本升級回支援的版本時，才能略過多個版本。 例如，從不支援的*1.10. x*升級 > 支援的*1.15。 x*可以完成。

## <a name="next-steps"></a>後續步驟

如需如何升級叢集的相關資訊，請參閱[升級 Azure Kubernetes Service (AKS) 叢集][aks-upgrade]。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
