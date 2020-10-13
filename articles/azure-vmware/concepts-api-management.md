---
title: 概念-API 管理
description: '瞭解 API 管理如何保護在 Azure VMware 解決方案虛擬機器 (Vm 上執行的 Api) '
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 346d0f795c3d19b115ced771991263cce2104217
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262972"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>用來發佈及保護在 Azure VMware 解決方案型 Vm 上執行之 Api 的 API 管理

Microsoft Azure [API 管理](https://azure.microsoft.com/services/api-management/) 可讓開發人員和 DevOps 團隊安全地發行給內部或外部取用者。

雖然在數個 Sku 中提供，但只有開發人員和 Premium Sku 允許 Azure 虛擬網路整合，以發佈在 Azure VMware 解決方案工作負載上執行的 Api。 這兩個 Sku 會安全地啟用 API 管理服務與後端之間的連線能力。 開發人員 SKU 適用于開發和測試，而 Premium SKU 適用于生產環境部署。

針對在 Azure VMware 解決方案虛擬機器 (Vm) 上執行的後端服務，API 管理中的設定預設會與內部部署後端服務相同。 針對內部和外部部署，API 管理會將負載平衡器的虛擬 IP (VIP) 設定為後端端點，以作為後端伺服器位於 Azure VMware 解決方案端上的 NSX Load Balancer 後方。

## <a name="external-deployment"></a>外部部署

外部部署會使用公用端點來發佈外部使用者使用的 Api。 開發人員和 DevOps 工程師可以透過 Azure 入口網站或 PowerShell 以及 API 管理開發人員入口網站來管理 Api。

外部部署圖表會顯示整個程式和相關的動作專案， (顯示在頂端) 。 執行者如下：

- **系統管理員 (s) ：** 代表系統管理員或 DevOps 團隊，它會透過 Azure 入口網站和自動化機制（例如 PowerShell 或 Azure DevOps）來管理 Azure VMware 解決方案。

- **使用者：**  代表公開之 Api 的取用者，並代表使用 Api 的使用者和服務。

流量會通過 API 管理實例，此實例會將插入至中樞虛擬網路的後端服務抽象化。 ExpressRoute 閘道會將流量路由傳送到 ExpressRoute 全球觸達通道，並到達 NSX Load Balancer 將連入流量分散到不同的後端服務實例。

API 管理具有 Azure 公用 API，並建議啟用 Azure DDOS 保護服務。 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="外部部署-Azure VMware 解決方案的 API 管理":::


## <a name="internal-deployment"></a>內部部署

內部部署會發佈由內部使用者或系統取用的 Api。 DevOps 小組和 API 開發人員使用與外部部署相同的管理工具和開發人員入口網站。

內部部署可以搭配 [Azure 應用程式閘道使用](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) ，以針對使用其功能的 API 建立公開且安全的端點，並建立可啟用不同案例的混合式部署。  API 會利用其功能，並建立可啟用不同案例的混合式部署。

* 使用相同的 API 管理資源，供內部和外部取用者取用。

* 具有單一 API 管理資源，並已定義並可供外部取用者使用的 Api 子集。

* 提供簡單的方法，讓您輕鬆地從公用網際網路切換存取 API 管理。

下列部署圖表顯示可為內部或外部的取用者，且每個類型都存取相同或不同的 Api。

在內部部署中，Api 會公開給相同的 API 管理實例。 在 API 管理之前，應用程式閘道會與 Azure Web 應用程式防火牆一起部署 (WAF) 功能已啟用，以及一組 HTTP 接聽程式 () 和規則來篩選流量，只會公開 Azure VMware 解決方案上所執行後端服務的子集。

* 內部流量會透過 ExpressRoute 閘道路由傳送至 Azure 防火牆，然後在 API 管理中建立流量規則或直接對 API 管理進行通訊。  

* 外部流量會透過應用程式閘道進入 Azure，而應用程式閘道會使用外部保護層來管理 API。


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="外部部署-Azure VMware 解決方案的 API 管理":::