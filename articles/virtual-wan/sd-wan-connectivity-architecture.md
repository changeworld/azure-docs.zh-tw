---
title: SD-WAN 連線架構
titleSuffix: Azure Virtual WAN
description: 瞭解如何使用 Azure 虛擬 WAN 來互連私人 SD-WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/08/2020
ms.author: cherylmc
ms.openlocfilehash: 34b2282421b9c95ad99ad040cb11847a30d3b52c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199988"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>SD-搭配 Azure 虛擬 WAN 的 WAN 連線架構

Azure 虛擬 WAN 是一種網路服務，透過單一操作介面整合許多雲端連線能力和安全性服務。 這些服務包含分支（透過站對站 VPN）、遠端使用者（點對站 vpn）、私人（ExpressRoute）連線能力、適用于 Vnet 的雲端可轉移連線、VPN 和 ExpressRoute 互連能力、路由、Azure 防火牆，以及私人連線的加密。

雖然 Azure 虛擬 WAN 本身是一種軟體定義的 WAN （SD WAN），但它也是設計來啟用與以內部部署為基礎的 SD 和服務的無縫互連。 我們的[虛擬 WAN](virtual-wan-locations-partners.md)生態系統和 Azure 網路管理服務合作夥伴[（msp）](../networking/networking-partners-msp.md)提供許多這類服務。 將其私人 WAN 轉換成 SD-WAN 的企業，在將其私用 SD-WAN 與 Azure 虛擬 WAN 進行互連時，會有選項。 企業可以從下列選項中選擇：

* 直接互連模型
* 間接互連模型
* 使用其最愛的受控服務提供者[MSP](../networking/networking-partners-msp.md)的受控混合式 WAN 模型

在所有這些情況下，虛擬 WAN 與 SD WAN 的互連與連線端類似，但在協調流程和操作端可能會有所不同。

## <a name="direct-interconnect-model"></a><a name="direct"></a>直接互連模型

![直接互連模型](./media/sd-wan-connectivity-architecture/direct.png)

在此架構模型中，SD-WAN 分支客戶部署設備（CPE）直接透過 IPsec 連線連接到虛擬 WAN 中樞。 Branch CPE 也可以透過私人 SD-WAN 連線到其他分支，或將虛擬 WAN 運用於分支對分支連接。 需要在 Azure 中存取其工作負載的分支，將能夠透過在虛擬 WAN 中樞內終止的 IPsec 通道，直接且安全地存取 Azure。

SD-WAN CPE 合作夥伴可以啟用自動化，以便從其各自的 CPE 裝置自動化通常單調乏味且容易出錯的 IPsec 連線。 自動化可讓 SD WAN 控制器透過虛擬 WAN API 與 Azure 通訊，以設定虛擬 WAN 網站，以及將必要的 IPsec 通道設定推送至分支 CPEs。 請參閱[自動化指導方針](virtual-wan-configure-automation-providers.md)，以瞭解各種 SD-WAN 合作夥伴的虛擬 WAN 互連自動化的說明。

SD-WAN CPE 會繼續是執行和強制執行「流量優化」的位置，以及路徑選取範圍。 

在此模型中，因為與虛擬 WAN 的連線是透過 IPsec 進行，而且 IPsec VPN 在虛擬 WAN VPN 閘道上終止，所以可能不支援以即時流量特性為基礎的部分廠商專屬流量優化。 例如，在分支 CPE 上選取的動態路徑是可行的，因為分支裝置會與另一個 SD WAN 節點交換各種網路封包資訊，因此識別最佳連結以在分支動態地使用各種優先順序的流量。 這項功能在需要最後英里優化（分支到最接近的 Microsoft POP）的區域中可能很有用。

透過虛擬 WAN，使用者可以取得 Azure 直接選取，這是跨多個 ISP 連結（從 branch CPE 到虛擬 WAN VPN 閘道）的原則式路徑選取範圍。 虛擬 WAN 可讓您從相同的 SD-WAN 分支 CPE 設定多個連結（路徑）;每個連結都代表從 SD-WAN CPE 的唯一公用 IP 到兩個不同的 Azure 虛擬 WAN VPN 閘道實例的雙重通道連線。 SD-WAN 廠商可以根據其原則引擎在 CPE 連結上設定的流量原則，對 Azure 執行最佳的路徑。

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>間接互連模型

![間接互連模型](./media/sd-wan-connectivity-architecture/indirect.png)

在此架構模型中，SD-WAN 分支 CPEs 會間接連線到虛擬 WAN 中樞。 如圖所示，SD WAN 虛擬 CPE 會部署在企業 VNet 中。 這個虛擬 CPE 接著會使用 IPsec 連接到虛擬 WAN 中樞。 虛擬 CPE 作為 Azure 的 SD WAN 閘道。 需要在 Azure 中存取其工作負載的分支，將可透過 v-CPE 閘道存取它們。

由於與 Azure 的連線是透過 v-CPE 閘道（NVA），從 Azure 工作負載 Vnet 到其他 SD-WAN 分支的所有流量都會透過 NVA。 在此模型中，使用者負責管理和操作 SD-WAN NVA，包括高可用性、擴充性和路由。
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>受控混合式 WAN 模型

![受控混合式 WAN 模型](./media/sd-wan-connectivity-architecture/hybrid.png)

在此架構模型中，企業可以利用受控服務提供者（MSP）合作夥伴所提供的受控 SD WAN 服務。 此模型類似于上述的直接或間接模型。 不過，在此模型中，sd-wan 設計、協調流程和作業是由 SD WAN 提供者所傳遞。

[Azure 網路功能 MSP 合作夥伴](../networking/networking-partners-msp.md)可以使用[azure 燈塔](https://azure.microsoft.com/services/azure-lighthouse/)，在企業客戶的 Azure 訂用帳戶中實行 SD-wan 和虛擬 WAN 服務，以及代表客戶操作端對端混合式 WAN。 這些 Msp 也可以將 Azure ExpressRoute 實作為虛擬 WAN，並以端對端受控服務的形式運作。

## <a name="additional-information"></a>其他資訊

* [包含常見問題](virtual-wan-faq.md)
* [解決遠端連線能力](work-remotely-support.md)
