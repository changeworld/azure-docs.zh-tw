---
title: Consul 概觀
description: 取得 Consul 的概觀
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773994"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>概觀

[Consul][consul] 是一個多資料中心感知的服務網路解決方案，可以在執行階段平台之間連線及保護服務。 [連線][consul-features]是提供服務網格功能的元件。

## <a name="architecture"></a>架構

根據預設，Consul 提供一個由 [Envoy][envoy-proxy] 型 [Sidecar][consul-sidecar] 所組成的資料平面。 Consul 具有插入式 Proxy 架構。 這些智慧型 Proxy 會控制進出網格應用程式和工作負載的所有網路流量。

控制平面會透過下列[元件][consul-architecture]管理設定和原則：

- **伺服器**：在伺服器模式中執行且會維護 Consul 叢集狀態的 Consul 代理程式。

- **用戶端**：在輕量型用戶端模式中執行的 Consul 代理程式。 每個計算節點都必須執行用戶端代理程式。 此用戶端會代理工作負載與 Consul 設定之間的設定和原則。 

下列架構圖示範資料平面和控制平面內的各種元件如何互動。

![Consul 元件和架構的概觀。](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>選取準則

針對您的工作負載評估 Consul 時，請務必了解並考慮下列各項：

- [Consul 準則](#consul-principles)
- [Capabilities](#capabilities)
- [案例](#scenarios)


### <a name="consul-principles"></a>Consul 準則

下列準則可[指引][consul-principles] Consul 專案：

- **API 驅動**：編纂所有設定和原則。

- **隨處均可執行與連線**：在執行階段平台 (Kubernetes、VM、無伺服器) 之間連線工作負載。

- **擴充和整合**：在基礎結構之間安全連線工作負載。


### <a name="capabilities"></a>功能

Consul 提供以下功能組合：

- **網格**：閘道 (多資料中心)、虛擬機器 (不在叢集節點中)、服務同步、內建偵錯選項

- **Proxy**：Envoy、內建 Proxy、插入式、適用於 Windows 工作負載的 l4 Proxy

- **流量管理**：路由、分割、解析

- **原則**：意圖、ACL

- **安全性**：授權、驗證、加密、SPIFFE 型身分識別、外部 CA (保存庫)、憑證管理及輪替

- **可檢視性**：計量、UI 儀表板、Prometheus、Grafana


### <a name="scenarios"></a>案例

Consul 非常適合且建議用於下列案例：

- 擴充現有 Consul 連線的工作負載

- 憑證管理方面的合規性需求

- 多叢集服務網格

- 要包含於服務網格中的 VM 型工作負載



## <a name="next-steps"></a>後續步驟

下列文件描述如何在 Azure Kubernetes Service (AKS) 上安裝 Consul：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service (AKS) 中安裝 Consul][consul-install]

您也可以進一步探索 Consul 功能和架構：

- [Consul 使用者入門教學課程][consul-getting-started]
- [Consul 功能][consul-features]
- [Consul 架構][consul-architecture]
- [Consul - 連線運作方式][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
