---
title: Linkerd 總覽
description: 取得 Linkerd 的總覽
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593762"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>概觀

[Linkerd][linkerd] 是容易使用且輕量的服務網格。

## <a name="architecture"></a>架構

Linkerd 提供由超細 [Linkerd][linkerd-proxy] 特殊化 proxy sidecar 組成的資料平面。 這些智慧型 Proxy 會控制進出網格應用程式和工作負載的所有網路流量。 Proxy 也會透過 [Prometheus][prometheus] 計量端點來公開計量。

控制平面會透過下列 [元件][linkerd-architecture]來管理設定和匯總的遙測：

- **控制器** -提供可驅動 Linkerd CLI 和儀表板的 api。 提供 proxy 的設定。

- 點**一下-建立**即時監看要求和回應。

- 身分**識別**：提供可讓服務之間進行 mTLS 的身分識別和安全性功能。

- **Web** -提供 Linkerd 儀表板。


下列架構圖示範資料平面和控制平面內的各種元件如何互動。


![Linkerd 元件和架構的總覽。](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>選取準則

針對您的工作負載評估 Linkerd 時，請務必瞭解並考慮下列各方面：

- [設計原則](#design-principles)
- [Capabilities](#capabilities)
- [案例](#scenarios)


### <a name="design-principles"></a>設計原則

下列設計原則會 [引導][design-principles] Linkerd 專案：

- **保持簡單** -必須便於使用和瞭解。

- **最小化資源需求** -強制執行最少的效能和資源成本。

- **只需工作** -不會中斷現有的應用程式，也不需要複雜的設定。


### <a name="capabilities"></a>功能

Linkerd 提供下列功能集：

- **網格** -內建的調試選項

- **流量管理** –分割、超時、重試、輸入

- **安全性** –加密 (的 mTLS) ，每24小時的憑證 autorotated

- **可檢視性** –黃金計量、點、追蹤、服務設定檔及每個路線計量、具有拓撲圖形的 web 儀表板、prometheus、grafana


### <a name="scenarios"></a>案例

Linkerd 適用于下列案例並建議您：

- 只搭配基本的功能需求集使用簡單

- 低延遲、低負荷，並著重于可檢視性和簡單的流量管理


## <a name="next-steps"></a>後續步驟

下列檔說明如何在 Azure Kubernetes Service (AKS) 上安裝 Linkerd：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service (AKS 中安裝 Linkerd) ][linkerd-install]

您也可以進一步探索 Linkerd 功能和架構：

- [Linkerd 功能][linkerd-features]
- [Linkerd 架構][linkerd-architecture]

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