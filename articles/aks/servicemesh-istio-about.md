---
title: 伊斯特奧概述
description: 獲取伊斯特奧的概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593895"
---
# <a name="istio"></a>伊斯特奧

## <a name="overview"></a>總覽

[Istio][istio]是一個功能齊全、可自訂且可擴展的服務網格。

## <a name="architecture"></a>架構

Istio 提供了一個資料平面，該平面由[基於特使][envoy-proxy]的側車組成。 這些智慧代理控制網格化應用和工作負載中進出的所有網路流量。

控制平面通過以下[元件][what-is-istio]管理配置、策略和遙測：

- **混合器**- 實施存取控制和使用策略。 從推送到[普羅米塞烏斯][prometheus]的代理中收集遙測資料。

- **試驗**- 為代理提供服務發現和流量管理原則/配置。

- **Citadel** - 提供允許服務之間的 mTLS 的標識和安全功能。

- **Galley** - 摘要，並為元件提供配置。

下面的體系結構圖演示了資料平面和控制平面中的各個元件如何交互。


![Istio 元件和體系結構概述。](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>選擇標準

在評估 Istio 的工作負載時，瞭解並考慮以下方面非常重要：

- [設計目標](#design-goals)
- [功能](#capabilities)
- [案例](#scenarios)


### <a name="design-goals"></a>設計目標

以下設計目標[指導][design-goals]了 Istio 專案：

- **最大化透明度**- 允許採用最少的工作量，從系統中獲得實際價值。

- **可擴充性**- 必須能夠成長和適應不斷變化的需求。

- **可攜性**- 在不同類型的環境中輕鬆運行 - 雲，本地。

- **策略一致性**- 不同資源的政策定義的一致性。


### <a name="capabilities"></a>功能

Istio 提供以下功能集：

- **網格**+ 閘道（多群集）、虛擬機器（網格擴展）

- **交通管理**– 路由、拆分、超時、斷路器、重試、入口、出口

- **策略**= 存取控制、速率限制、配額、自訂策略配接器

- **安全性**+ 身份驗證 （jwt）、授權、加密 （mTLS）、外部 CA（HashiCorp 保險庫）

- **可觀測性**– 黃金指標、鏡像、跟蹤、自訂配接器、普羅米古斯、格拉法納

### <a name="scenarios"></a>案例

Istio 非常適合並針對以下方案建議：

- 需要可擴充性和豐富的功能集

- 網格擴展以包括基於 VM 的工作負載

- 多叢集服務網格

## <a name="next-steps"></a>後續步驟

以下文檔介紹如何在 Azure 庫伯奈斯服務 （AKS） 上安裝 Istio：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service (AKS) 中安裝 Istio][istio-install]

您還可以進一步探索 Istio 概念和其他部署模型：

- [伊斯特奧概念][what-is-istio]
- [Istio 部署模型][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
