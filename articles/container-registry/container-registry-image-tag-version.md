---
title: 映射標記最佳做法
description: 將映射推送至 Azure container registry 並從中提取映射時，標記和版本控制 Docker 容器映射的最佳做法
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75445742"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>標記和版本設定容器映射的建議

將容器映射部署至容器登錄並加以部署時，您需要有映射標記和版本設定的策略。 本文討論兩種方法，並在容器生命週期中各自納入：

* **穩定標記**-您重複使用的標記，例如，用來表示主要或次要版本，例如*mycontainerimage： 1.0*。
* **唯一標記**-您推送至登錄之每個映射的不同標記，例如*mycontainerimage： abc123*。

## <a name="stable-tags"></a>穩定標記

**建議**：使用穩定標記來維護容器組建的**基底映射**。 避免部署具有穩定的標籤，因為這些標記會繼續接收更新，而且可能會在生產環境中導致不一致。

*穩定標記*表示開發人員或組建系統可以繼續提取特定標籤，這會繼續取得更新。 穩定並不表示內容已凍結。 相反地，「穩定」意指此版本的「映射」應該是穩定的。 為了維持「穩定」，它可能會被服務以套用安全性修補程式或架構更新。

### <a name="example"></a>範例

架構小組發行1.0 版。 他們知道他們會送出更新，包括次要更新。 若要針對指定的主要和次要版本支援穩定的標記，它們有兩組穩定的標記。

* `:1`–主要版本的穩定標記。 `1`代表「最新」或「最新」 1. * 版本。
* `:1.0`-1.0 版的穩定標記，可讓開發人員系結至1.0 的更新，而不會在發行時向前復原至1.1。

`:latest`無論目前的主要版本為何，小組也會使用指向最新穩定標記的標記。

當基底映射更新可供使用，或架構的任何服務版本時，具有穩定標記的影像會更新為最新的摘要，代表該版本的最新穩定版本。

在此情況下，會持續維護主要和次要標記。 從基底映射案例中，這可讓映射擁有者提供服務的映射。

### <a name="delete-untagged-manifests"></a>刪除未標記的資訊清單

如果更新具有穩定標籤的映射，則先前標記的影像會取消標示，因而產生孤立的影像。 先前的映射資訊清單和唯一層資料會保留在登錄中。 若要維護您的登錄大小，您可以定期刪除穩定映射更新所產生的未標記資訊清單。 例如，[自動清除](container-registry-auto-purge.md)早于指定期間的未標記資訊清單，或為未標記的資訊清單設定[保留原則](container-registry-retention-policy.md)。

## <a name="unique-tags"></a>唯一標記

**建議**.. 針對**部署**使用唯一標記，特別是在可在多個節點上調整的環境中。 您可能想要故意部署一致版本的元件。 如果您的容器重新開機，或協調器相應放大更多實例，則您的主機不會意外提取較新的版本，與其他節點不一致。

唯一標記只是表示推送至登錄的每個映射都有唯一的標記。 標記不會重複使用。 您可以遵循幾種模式來產生唯一的標記，包括：

* **日期時間戳記**-這種方法相當常見，因為您可以清楚地分辨映射的建立時間。 但是，如何將它與您的組建系統相互關聯？ 您是否必須尋找已同時完成的組建？ 您的時區是什麼？ 您的組建系統是否已向 UTC 校正？
* **Git 認可**–此方法會運作，直到您開始支援基底映射更新為止。 如果基底映射更新發生，您的組建系統會使用與上一個組建相同的 Git 認可來啟動。 不過，基底映射具有新的內容。 一般來說，Git commit 會*提供一個不穩定的標記*。
* **資訊清單摘要**-推送至容器登錄的每個容器映射都會與資訊清單相關聯，並以唯一的 SHA-256 雜湊或摘要來識別。 唯一的是，摘要很長、不易閱讀，而且與您的組建環境不相關。
* **組建識別碼**-這個選項可能最適合，因為它可能是累加的，而且可讓您與特定的組建相互關聯，以尋找所有成品和記錄。 不過，就像資訊清單摘要一樣，人可能很難以閱讀。

  如果您的組織有數個組建系統，請在標記前面加上組建系統名稱，這是此選項的變化： `<build-system>-<build-id>` 。 例如，您可以區分 API 小組的 Jenkins 組建系統和 web 小組的 Azure Pipelines 組建系統的組建。

### <a name="lock-deployed-image-tags"></a>鎖定已部署的映射標記

建議的最佳作法是將其屬性設為，以[鎖定](container-registry-image-lock.md)任何已部署的影像標記 `write-enabled` `false` 。 這種做法可防止您不小心移除登錄中的映射，而且可能會中斷您的部署。 您可以在發行管線中包含鎖定步驟。

鎖定已部署的映射仍然可讓您使用 Azure Container Registry 功能來維護您的登錄，從登錄中移除其他已解除部署的映射。 例如，[自動清除](container-registry-auto-purge.md)未標記的資訊清單或已解除鎖定的映射超過指定的持續時間，或為未標記的資訊清單設定[保留原則](container-registry-retention-policy.md)。

## <a name="next-steps"></a>後續步驟

如需本文中概念的詳細討論，請參閱[Docker 標記的文章：標記和版本控制 docker 映射的最佳作法](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)。

若要協助將 Azure container registry 的效能和成本效益發揮到最大，請參閱[Azure Container Registry 的最佳做法](container-registry-best-practices.md)。

<!-- IMAGES -->


<!-- LINKS - Internal -->

