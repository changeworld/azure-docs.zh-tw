---
title: Istio 的總覽
description: 取得 Istio 的總覽
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593895"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>概觀

[Istio][istio]是功能完整、可藉且可擴充的服務網格。

## <a name="architecture"></a>架構

Istio 提供由以[Envoy][envoy-proxy]為基礎的 sidecar 所組成的資料平面。 這些智慧型 proxy 會控制進出網狀應用程式和工作負載的所有網路流量。

控制平面會透過下列[元件][what-is-istio]來管理設定、原則和遙測：

- **混音**器-強制執行存取控制和使用原則。 從已推送至[Prometheus][prometheus]的 proxy 收集遙測。

- **試驗**-提供 proxy 的服務探索和流量管理原則/設定。

- **Citadel** -提供身分識別和安全性功能，允許服務之間的 mTLS。

- 向外**條**-抽象化並提供元件的設定。

下列架構圖表示范資料平面和控制平面內的各種元件如何互動。


![Istio 元件和架構的總覽。](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>選取準則

針對您的工作負載評估 Istio 時，請務必瞭解並考慮下列各方面：

- [設計目標](#design-goals)
- [功能](#capabilities)
- [案例](#scenarios)


### <a name="design-goals"></a>設計目標

下列設計目標會[引導][design-goals]Istio 專案：

- **最大化透明度**-允許採用最少量的工作，以從系統中取得真正的價值。

- 擴充性-必須能夠隨著不斷**變化的需求**成長及調整。

- 可**移植性**-在不同類型的環境中輕鬆執行-雲端、內部部署。

- **原則一致性**-跨各種資源的原則定義中的一致性。


### <a name="capabilities"></a>功能

Istio 提供下列功能集：

- **網格**–閘道（多叢集）、虛擬機器（網格擴充）

- **流量管理**–路由，分割，超時，斷路器，重試，輸入，輸出

- **原則**–存取控制、速率限制、配額、自訂原則介面卡

- **安全性**–驗證（jwt）、authorisation、加密（mTLS）、外部 CA （HashiCorp Vault）

- **可檢視性**–黃金標準，鏡像，追蹤，自訂介面卡，prometheus，grafana

### <a name="scenarios"></a>案例

Istio 非常適合和建議用於下列案例：

- 需要擴充性和豐富的功能集

- 網格擴充以包含以 VM 為基礎的工作負載

- 多叢集服務網格

## <a name="next-steps"></a>後續步驟

下列檔會說明如何在 Azure Kubernetes Service （AKS）上安裝 Istio：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service (AKS) 中安裝 Istio][istio-install]

您也可以進一步探索 Istio 概念和其他部署模型：

- [Istio 概念][what-is-istio]
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
