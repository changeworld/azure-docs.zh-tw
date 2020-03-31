---
title: 連結概述
description: 獲取連結的概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593762"
---
# <a name="linkerd"></a>林克德

## <a name="overview"></a>總覽

[Linkerd][linkerd]是一個便於使用且輕量的服務網格。

## <a name="architecture"></a>架構

Linkerd 提供由超輕型[Linkerd][linkerd-proxy]專用代理側車組成的資料平面。 這些智慧代理控制網格化應用和工作負載中進出的所有網路流量。 代理還通過[Prometheus][prometheus]指標終結點公開指標。

控制平面通過以下[元件][linkerd-architecture]管理配置和聚合遙測：

- **控制器**- 提供驅動連結 CLI 和儀表板的 api。 為代理提供配置。

- **點擊**- 建立對請求和回應的即時監視。

- **標識**- 提供允許服務之間 mTLS 的標識和安全功能。

- **Web** - 提供連結儀表板。


下面的體系結構圖演示了資料平面和控制平面中的各個元件如何交互。


![連結元件和體系結構概述。](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>選擇標準

在評估 Linkerd 的工作負載時，瞭解並考慮以下方面非常重要：

- [設計原則](#design-principles)
- [功能](#capabilities)
- [案例](#scenarios)


### <a name="design-principles"></a>設計原則

以下設計原則[指導][design-principles]連結專案：

- **保持簡單**- 必須便於使用和理解。

- **最小化資源需求**- 將性能和資源成本降至最低。

- **只需工作**- 不要破壞現有應用程式，也不需要複雜的配置。


### <a name="capabilities"></a>功能

Linkerd 提供以下功能集：

- **網格**= 內置調試選項

- **流量管理**– 拆分、超時、重試、入口

- **安全性**+ 加密 （mTLS），證書每 24 小時自動旋轉一次

- **可觀測性**– 黃金指標、點擊、跟蹤、服務設定檔和每個路由指標、帶拓撲圖的 Web 儀表板、原型、grafana


### <a name="scenarios"></a>案例

Linkerd 非常適合並針對以下方案建議：

- 簡單使用，只需基本功能要求集

- 低延遲、低開銷，注重可觀察性和簡單的流量管理


## <a name="next-steps"></a>後續步驟

以下文檔介紹如何在 Azure 庫伯奈斯服務 （AKS） 上安裝 Linkerd：

> [!div class="nextstepaction"]
> [在 Azure 庫伯奈斯服務 （AKS） 中安裝連結][linkerd-install]

您還可以進一步探索 Linkerd 功能和體系結構：

- [連結功能][linkerd-features]
- [連結式體系結構][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md