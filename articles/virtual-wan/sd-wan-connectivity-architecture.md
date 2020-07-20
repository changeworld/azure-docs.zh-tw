---
title: SD-WAN 連線架構
titleSuffix: Azure Virtual WAN
description: 瞭解如何使用 Azure 虛擬 WAN 來互連私人 SD-WAN
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: sukishen
ms.openlocfilehash: 7b134e8ef4e09f1506f1d548ffb7579dcf65fbdd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753320"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>使用 Azure 虛擬 WAN 的 SD-WAN 連線架構

Azure 虛擬 WAN 是一種網路服務，透過單一操作介面整合許多雲端連線能力和安全性服務。 這些服務包含分支 (站對站 VPN)、遠端使用者 (點對站 VPN)、私人 (ExpressRoute) 連線、適用於 Vnet 的雲端內可轉移連線、VPN 和 ExpressRoute 互連能力、路由、Azure 防火牆，以及適用於私人連線的加密。

雖然 Azure 虛擬 WAN 本身是軟體定義的 WAN (SD-WAN)，但其設計目的也是為了可與內部部署 SD-WAN 技術和服務順暢地進行互連。 這類服務大多數由我們的[虛擬 WAN](virtual-wan-locations-partners.md) 生態系統和 Azure 網路受控服務合作夥伴 [(MSP)](../networking/networking-partners-msp.md) 所提供。 將私人 WAN 轉換至 SD-WAN 的企業在將其私人 SD-WAN 與 Azure 虛擬 WAN 進行互連時，有數種選擇。 企業可從下列選項中選擇：

* 直接互連模型
* 間接互連模型
* 使用其最常用的受控服務提供者 [MSP](../networking/networking-partners-msp.md) 的受控混合式 WAN 模型

在所有這些情況下，虛擬 WAN 與 SD-WAN 的互連從連線方面來看是相似的，但在協調流程和操作方面可能有所不同。

## <a name="direct-interconnect-model"></a><a name="direct"></a>直接互連模型

![直接互連模型](./media/sd-wan-connectivity-architecture/direct.png)

在此架構模式中，SD-WAN 分支用戶端設備 (CPE) 會透過 IPsec 連線直接連線至虛擬 WAN 中樞。 分支 CPE 也可以透過私人 SD-WAN 連線到其他分支，或利用虛擬 WAN 以進行分支對分支連線。 需要在 Azure 中存取其工作負載的分支將能夠透過終止於虛擬 WAN 中樞內的 IPsec 通道，直接安全地存取 Azure。

SD-WAN CPE 合作夥伴可以啟用自動化，以便從其各自的 CPE 裝置上自動完成通常繁瑣且容易出錯的 IPsec 連線。 自動化可讓 SD WAN 控制器透過虛擬 WAN API 與 Azure 通訊，以設定虛擬 WAN 網站，並將必要的 IPsec 通道設定推送至分支 CPE。 如需各種 SD-WAN 合作夥伴的虛擬 WAN 互連自動化的說明，請參閱[自動化指導方針](virtual-wan-configure-automation-providers.md)。

SD-WAN CPE 仍然是流量最佳化以及路徑選擇的實作和強制執行的所在位置。 

在此模型中，因為與虛擬 WAN 的連線是透過 IPsec 進行，且 IPsec VPN 是終止於虛擬 WAN VPN 閘道上，所以可能不支援某些廠商以即時流量特性為基礎的專屬流量最佳化。 例如，在分支 CPE 的動態路徑選擇是可行的，因為分支裝置會與另一個 SD WAN 節點交換各種網路封包資訊，因此，可於分支中動態識別最佳連結，以用於各種已排定優先順序的流量。 此功能在最後關鍵最佳化的所在區域中 (分支到最接近的 Microsoft POP) 可能會很有幫助。

透過虛擬 WAN，使用者可以取得 Azure 路徑選擇，這是跨多個 ISP 連結 (從分支 CPE 到虛擬 WAN VPN 閘道) 的原則式路徑選則。 虛擬 WAN 可讓您從相同的 SD-WAN 分支 CPE 設定多個連結 (路徑)；每個連結代表從 SD-WAN CPE 的唯一公用 IP 到兩個不同的 Azure 虛擬 WAN VPN 閘道執行個體的雙通道連線。 SD-WAN 廠商可以根據其原則引擎在 CPE 連結上設定的流量原則，對 Azure 實作最佳路徑。 在 Azure 端，所有傳入的連線都會受到同等對待。

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>間接互連模型

![間接互連模型](./media/sd-wan-connectivity-architecture/indirect.png)

在此架構模型中，SD-WAN 分支 CPE 會間接連線到虛擬 WAN 中樞。 如圖所示，SD WAN 虛擬 CPE 部署在企業 VNet 中。 接下來，這個虛擬 CPE 會使用 IPsec 連線到虛擬 WAN 中樞。 使用虛擬 CPE 作為進入 Azure 的 SD WAN 閘道。 需要在 Azure 中存取其工作負載的分支將能夠透過 v-CPE 閘道存取工作負載。

由於與 Azure 的連線是透過 v-CPE 閘道 (NVA) 進行的，因此，所有進出 Azure 工作負載 VNet 到其他 SD-WAN 分支的流量都要透過 NVA 進行。 在此模型中，使用者負責管理和操作 SD-WAN NVA，包括高可用性、可擴縮性和路由。
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>受控混合式 WAN 模型

![受控混合式 WAN 模型](./media/sd-wan-connectivity-architecture/hybrid.png)

在此架構模型中，企業可以利用受控服務提供者 (MSP) 合作夥伴所提供的受控 SD WAN 服務。 此模型與上述的直接或間接模型類似。 不過，在此模型中，SD-WAN 設計、協調流程和作業是由 SD WAN 提供者所傳遞。

[Azure 網路 MSP 合作夥伴](../networking/networking-partners-msp.md)可使用 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/)，以在企業客戶的 Azure 訂閱中實作 SD-WAN 和虛擬 WAN 服務，以及代表客戶操作端對端混合式 WAN。 這些 MSP 也可以將 Azure ExpressRoute 實作到虛擬 WAN 中，並以端對端受控服務的形式進行操作。

## <a name="additional-information"></a>其他資訊

* [包含常見問題集](virtual-wan-faq.md)
* [解決遠端連線能力](work-remotely-support.md)
