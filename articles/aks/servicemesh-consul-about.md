---
title: Consul 的總覽
description: 取得 Consul 的總覽
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77594204"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>概觀

[Consul][consul]是多個資料中心感知服務網路解決方案，可在執行時間平臺之間連接及保護服務。 [Connect][consul-features]是提供服務網格功能的元件。

## <a name="architecture"></a>架構

根據預設，Consul 會提供由[Envoy][envoy-proxy]型[sidecar][consul-sidecar]所組成的資料平面。 Consul 具有可插入的 proxy 架構。 這些智慧型 proxy 會控制進出網狀應用程式和工作負載的所有網路流量。

控制平面會透過下列[元件][consul-architecture]來管理設定和原則：

- **伺服器**-在伺服器模式下執行且會維護 Consul 叢集狀態的 Consul 代理程式。

- **用戶端**-在輕量用戶端模式中執行的 Consul 代理程式。 每個計算節點都必須執行用戶端代理程式。 此用戶端會代理程式在工作負載與 Consul 設定之間的設定和原則。 

下列架構圖表示范資料平面和控制平面內的各種元件如何互動。

![Consul 元件和架構的總覽。](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>選取準則

針對您的工作負載評估 Consul 時，請務必瞭解並考慮下列各方面：

- [Consul 原則](#consul-principles)
- [功能](#capabilities)
- [案例](#scenarios)


### <a name="consul-principles"></a>Consul 原則

下列原則會[引導][consul-principles]Consul 專案：

- **API 驅動**-編寫所有設定和原則。

- **執行並聯機到任何位置**-跨執行時間平臺（Kubernetes、Vm、無伺服器）連接工作負載。

- **擴充和整合**-安全地連接基礎結構之間的工作負載。


### <a name="capabilities"></a>功能

Consul 提供下列功能集：

- **網格**–閘道（多個資料中心）、虛擬機器（叢集節點不足）、服務同步處理、內建調試選項

- Proxy **– Envoy** 、內建 proxy、插即用、適用于 Windows 工作負載的 l4 proxy

- **流量管理**–路由、分割、解析

- **原則**-意圖、acl

- **安全性**– authorisation、驗證、加密、SPIFFE 為基礎的身分識別、外部 CA （保存庫）、憑證管理和輪替

- **可檢視性**–計量、ui 儀表板、prometheus、grafana


### <a name="scenarios"></a>案例

Consul 非常適合和建議用於下列案例：

- 擴充現有的 Consul 連線工作負載

- 憑證管理方面的合規性需求

- 多重叢集服務網格

- 以 VM 為基礎的工作負載要包含在服務網格中



## <a name="next-steps"></a>後續步驟

下列檔會說明如何在 Azure Kubernetes Service （AKS）上安裝 Consul：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service 中安裝 Consul （AKS）][consul-install]

您也可以進一步探索 Consul 功能和架構：

- [Consul 功能][consul-features]
- [Consul 架構][consul-architecture]
- [Consul-Connect 的運作方式][consul-how-connect-works]

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
