---
title: 領事概述
description: 獲得領事概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594204"
---
# <a name="consul"></a>領事

## <a name="overview"></a>總覽

[Consul][consul]是一種多資料中心感知服務網路解決方案，用於跨運行時平臺連接和保護服務。 [連接][consul-features]是提供服務網格功能的元件。

## <a name="architecture"></a>架構

預設情況下，領事提供由[基於特使][envoy-proxy]的[側車][consul-sidecar]組成的資料平面。 領事具有可插拔的代理體系結構。 這些智慧代理控制網格化應用和工作負載中進出的所有網路流量。

控制平面通過以下[元件][consul-architecture]管理配置和策略：

- **伺服器**- 在伺服器模式下運行的領事代理，維護領事群集狀態。

- **用戶端**- 在羽量級用戶端模式下運行的領事代理。 每個計算節點都必須運行一個用戶端代理。 此用戶端在工作負載和領事配置之間代理配置和策略。 

下面的體系結構圖演示了資料平面和控制平面中的各個元件如何交互。

![領事元件和架構概述。](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>選擇標準

在評估領事的工作量時，瞭解並考慮以下方面非常重要：

- [領事原則](#consul-principles)
- [功能](#capabilities)
- [案例](#scenarios)


### <a name="consul-principles"></a>領事原則

以下原則[指導][consul-principles]領事專案：

- **API 驅動**- 編纂所有配置和策略。

- **隨時隨地運行和連接**- 跨運行時平臺（庫伯內特、VM、無伺服器）連接工作負載。

- **擴展和集成**- 跨基礎架構安全地連接工作負載。


### <a name="capabilities"></a>功能

領事提供以下功能集：

- **網格**+ 閘道（多資料中心）、虛擬機器（叢集節點外）、服務同步、內置調試選項

- **代理**= 特使、內置代理、可插拔的 l4 代理，適用于 Windows 工作負載

- **流量管理**– 路由、拆分、解析

- **政策**= 意圖，ACL

- **安全性**– 授權、身份驗證、加密、基於 SPIFFE 的身份、外部 CA （Vault）、證書管理和輪換

- **可觀測性**– 指標、ui 儀表板、普羅米圖斯、格拉法納


### <a name="scenarios"></a>案例

領事非常適合並針對以下情況提出建議：

- 擴展現有領事連接工作負載

- 有關證書管理的合規性要求

- 多叢集服務網格

- 要包含在服務網格中的基於 VM 的工作負載



## <a name="next-steps"></a>後續步驟

以下文檔介紹如何在 Azure 庫伯奈斯服務 （AKS） 上安裝駐外領事：

> [!div class="nextstepaction"]
> [在 Azure 庫伯奈斯服務 （AKS） 中安裝領事][consul-install]

您還可以進一步探索領事功能和建築：

- [領事特寫][consul-features]
- [領事架構][consul-architecture]
- [領事 - 連接的工作原理][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
