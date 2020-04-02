---
title: 連接到 Azure 實驗室服務中的對等網路 |微軟文件
description: 瞭解如何將實驗室網路與其他網路作為對等體連接。 例如,將本地學校/大學網路與 Azure 中的實驗室虛擬網路連接。
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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 224526efc2152e0b788c5cbc7f3bd60bb3363c1a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545717"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>將實驗室網路與 Azure 實驗室服務中的對等虛擬網路連線 
本文提供有關將實驗室網路與其他網路對等的資訊。 

## <a name="overview"></a>概觀
虛擬網路對等互連使您能夠無縫連接 Azure 虛擬網路。 經過對等互連後，所有虛擬網路就可以作為一個整體來進行連線。 對等虛擬網路中虛擬機器之間的流量通過 Microsoft 骨幹基礎結構路由,就像流量透過專用 IP 位址在同一虛擬網路中的虛擬機器之間路由一樣。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

在某些情況下,您可能需要將實驗室網路與對等虛擬網路連接,包括以下方案:

- 實驗室中的虛擬機具有連線到本地授權伺服器以取得授權的軟體
- 實驗室中的虛擬機需要訪問大學網路共享上的數據集(或任何其他檔)。 

某些本地網路透過[ExpressRoute](../../expressroute/expressroute-introduction.md)或[虛擬網路閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)連接到 Azure 虛擬網路。 這些服務必須在 Azure 實驗室服務之外設置。 要瞭解有關使用 ExpressRoute 將本地網路連線到 Azure 的詳細資訊,請參閱[ExpressRoute 概述](../../expressroute/expressroute-introduction.md)。 對於使用虛擬網路閘道的本地連接,閘道、指定的虛擬網路和實驗室帳戶必須都位於同一區域。

> [!NOTE]
> 創建與實驗室帳戶對等的 Azure 虛擬網路時,瞭解虛擬網路區域如何影響創建教室實驗室的位置非常重要。  有關詳細資訊,請參閱管理員指南有關[區域\位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)的部分。

## <a name="configure-at-the-time-of-lab-account-creation"></a>建立實驗室帳戶時進行設定
在新的實驗室帳戶建立期間,可以選擇在 **「高級**」選項卡上的**對等虛擬網路**下拉清單中顯示的現有虛擬網路。所選虛擬網路連接到實驗室帳戶下創建的實驗室(對等)。 進行此更改後創建的所有實驗室中的虛擬機都將有權訪問對等式虛擬網路上的資源。 

還有一項規定為實驗室提供**虛擬機器的位址範圍**。 如果提供位址範圍,實驗室帳戶下的所有虛擬機都將在該地址範圍內創建。 位址範圍應以 CIDR 表示法(例如 10.20.0.0/20)表示,不得與任何現有位址範圍重疊。 提供位址範圍時,考慮將在實驗室中創建的虛擬機數量並提供位址範圍以容納該範圍非常重要。 對於給定範圍,將顯示它可以容納的實驗室數量。

![選擇要對等的 VNet](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 有關創建實驗室帳戶的詳細分步說明,請參閱[設置實驗室帳戶](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>建立實驗室後設定
如果在創建實驗室帳戶時未設置對等網路,則可以從**實驗室帳戶**頁的 **「實驗室配置**」選項卡啟用相同的屬性。 對此設置所做的更改僅適用於更改後創建的實驗室。 如您在映像中看到的,您可以為實驗室帳戶中的實驗室啟用或關閉**對等虛擬網路**。 

![建立實驗室後啟用或關閉 VNet 對等互連](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

當您為**對等虛擬網路**欄位選擇虛擬網路時,將禁用「**允許實驗室建立者選擇實驗室位置**」選項。 這是因為實驗室帳戶中的實驗室必須與實驗室帳戶位於同一區域,以便它們與對等虛擬網路中的資源進行連接。 

> [!IMPORTANT]
> 此設置更改僅適用於進行更改後創建的實驗室,不適用於現有實驗室。 


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將分享影像庫附加到實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [檢視實驗室的防火牆設定](how-to-configure-firewall-settings.md)
- [為實驗室設定其他設定](how-to-configure-lab-accounts.md)
