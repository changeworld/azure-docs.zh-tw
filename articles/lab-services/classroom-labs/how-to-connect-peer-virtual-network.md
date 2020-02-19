---
title: 連接到 Azure 實驗室服務中的對等網路 |Microsoft Docs
description: 瞭解如何將您的實驗室網路與其他網路連線，做為對等。 例如，將您的內部部署學校/大學網路與實驗室在 Azure 中的虛擬網路連線。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443175"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>將實驗室的網路與 Azure 實驗室服務中的對等虛擬網路連線 
本文提供與您的實驗室網路與其他網路對等互連的相關資訊。 

## <a name="overview"></a>概觀
虛擬網路對等互連可讓您順暢地連接 Azure 虛擬網路。 經過對等互連後，所有虛擬網路就可以作為一個整體來進行連線。 對等互連虛擬網路中虛擬機器之間的流量會透過 Microsoft 骨幹基礎結構路由傳送，就像流量只會透過私人 IP 位址在相同虛擬網路中的虛擬機器之間路由傳送。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

在某些案例中，您可能需要將實驗室的網路與對等虛擬網路連線，包括下列各項：

- 實驗室中的虛擬機器具有連接到內部部署授權伺服器以取得授權的軟體
- 實驗室中的虛擬機器需要存取大學網路共用上的資料集（或任何其他檔案）。 

某些內部部署網路會透過[ExpressRoute](../../expressroute/expressroute-introduction.md)或[虛擬網路閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)連線至 Azure 虛擬網路。 這些服務必須在 Azure 實驗室服務外部設定。 若要深入瞭解如何使用 ExpressRoute 將內部部署網路連接至 Azure，請參閱[ExpressRoute 總覽](../../expressroute/expressroute-introduction.md)。 針對使用虛擬網路閘道的內部部署連線能力，閘道、指定的虛擬網路和實驗室帳戶必須全部位於相同的區域。

## <a name="configure-at-the-time-of-lab-account-creation"></a>在建立實驗室帳戶時設定
在建立新的實驗室帳戶期間，您可以在 [ **Advanced** ] 索引標籤的 [**對等虛擬網路**] 下拉式清單中，挑選顯示的現有虛擬網路。選取的虛擬網路已連接（對等互連）至實驗室帳戶所建立的實驗室。 在進行這項變更之後，在實驗室中建立的所有虛擬機器都可以存取對等互連虛擬網路上的資源。 

![選取 VNet 對等互連](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 如需建立實驗室帳戶的詳細逐步指示，請參閱[設定實驗室帳戶](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>在建立實驗室之後設定
如果您在建立實驗室帳戶時未設定對等網路，則可以從 [**實驗室帳戶**] 頁面的 [**實驗室**設定] 索引標籤中啟用相同的屬性。 對此設定所做的變更，只會套用到變更後所建立的實驗室。 如您在映射中所見，您可以在實驗室帳戶中啟用或停用實驗室的「**對等虛擬網路**」。 

![建立實驗室之後，啟用或停用 VNet 對等互連](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

當您選取虛擬網路作為 [**對等虛擬網路**] 欄位時，會停用 [**允許實驗室建立者選擇實驗室位置**] 選項。 這是因為實驗室帳戶中的實驗室必須與實驗室帳戶位在相同的區域中，才能與對等虛擬網路中的資源連線。 

> [!IMPORTANT]
> 此設定變更僅適用于在進行變更時所建立的實驗室，而不會套用至現有的實驗室。 


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者選擇實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將共用映射資源庫連結至實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設定](how-to-configure-firewall-settings.md)
- [設定實驗室的其他設定](how-to-configure-lab-accounts.md)