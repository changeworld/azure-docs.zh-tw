---
title: 概念-API 管理
description: 瞭解 API 管理如何保護在 Azure VMware 解決方案（AVS）虛擬機器（Vm）上執行的 Api
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306708"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>API 管理，用以發佈和保護在以 AVS 為基礎的 Vm 上執行的 Api

Microsoft Azure [API 管理](https://azure.microsoft.com/services/api-management/)可讓開發人員和 DevOps 小組安全地向內部或外部取用者發佈。

雖然在數個 Sku 中提供，但只有開發人員和 Premium Sku 允許 Azure 虛擬網路整合，才能發佈在 Azure VMware 解決方案（AVS）工作負載上執行的 Api。 這兩個 Sku 會安全地啟用 API 管理服務與後端之間的連線。 開發人員 SKU 適用于開發和測試，而高階 SKU 適用于生產環境部署。

對於在 AVS 虛擬機器（Vm）上執行的後端服務，API 管理中的設定預設會與內部部署後端服務相同。 針對內部和外部部署，API 管理會設定負載平衡器的虛擬 IP （VIP），做為後端伺服器放在 AVS 端 Load Balancer 在 NSX 後方的後端端點。

## <a name="external-deployment"></a>外部部署

外部部署會發佈外部使用者使用公用端點所使用的 Api。 開發人員和 DevOps 工程師可以透過 Azure 入口網站或 PowerShell，以及 API 管理開發人員入口網站來管理 Api。

外部部署圖表會顯示整個進程和相關的執行者（顯示在頂端）。 執行者如下：

- **系統管理員：** 代表系統管理員或 DevOps 小組，透過 Azure 入口網站和自動化機制（例如 PowerShell 或 Azure DevOps）來管理 AVS。

- **使用者：** 代表公開之 Api 的取用者，並代表取用 Api 的使用者和服務。

流量會通過 API 管理實例，這會將後端服務抽象化，並插入中樞虛擬網路。 ExpressRoute 閘道會將流量路由傳送至 ExpressRoute 全域觸達通道，並到達 NSX Load Balancer 將連入流量分散到不同的後端服務實例。

API 管理具有 Azure 公用 API，建議您啟用 Azure DDOS 保護服務。 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="外部部署-適用于 AVS 的 API 管理":::


## <a name="internal-deployment"></a>內部部署

內部部署會發行內部使用者或系統所使用的 Api。 DevOps 小組和 API 開發人員使用與外部部署相同的管理工具和開發人員入口網站。

內部部署可以[與 Azure 應用程式閘道搭配使用](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)，以建立 API 的公用安全端點，利用其功能並建立可啟用不同案例的混合式部署。  此 API 會利用其功能，並建立可啟用不同案例的混合式部署。

* 使用相同的 API 管理資源，供內部和外部取用者取用。

* 具有單一 API 管理資源，其中包含定義的 Api 子集，並可供外部取用者使用。

* 提供一種簡單的方式，可從公用網際網路開啟和關閉 API 管理的存取權。

下列部署圖表顯示可以是內部或外部的取用者，每個類型都存取相同或不同的 Api。

在內部部署中，Api 會公開至相同的 API 管理實例。 在 API 管理的前方，應用程式閘道會部署並啟用 Azure Web 應用程式防火牆（WAF）功能，以及一組 HTTP 接聽項和規則來篩選流量，只公開在 AVS 上執行的後端服務子集。

* 內部流量會透過 ExpressRoute 閘道路由至 Azure 防火牆，然後在建立流量規則或直接到 API 管理時，傳送至 API 管理。  

* 外部流量會透過應用程式閘道進入 Azure，這會使用適用于 API 管理的外部保護層。


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="內部部署-適用于 AVS 的 API 管理":::