---
title: 連線到 Azure Lab Services 中的對等網路 | Microsoft Docs
description: 了解如何將實驗室網路與其他網路連線，成為對等項目。 例如，在 Azure 中將內部部署組織/大學網路與實驗室的虛擬網路連線。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 20e47113d5c2439c9c8ea355288442b5f41d90ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445826"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>將實驗室的網路與 Azure Lab Services 中的對等虛擬網路連線

本文提供將實驗室網路與其他網路對等互連的相關資訊。

## <a name="overview"></a>概觀

虛擬網路對等互連可讓您完美地連線 Azure 虛擬網路。 經過對等互連後，所有虛擬網路就可以作為一個整體來進行連線。 在對等互連虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構路由傳送，其原理就像在相同虛擬網路中的虛擬機器之間，流量只會透過私人 IP 位址來路由傳送。 如需詳細資訊，請參閱[虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)。

在某些情境中，您可能需要將實驗室的網路與對等虛擬網路連線，包括下列情境：

- 實驗室中的虛擬機器具有連線到內部部署授權伺服器以取得授權的軟體。
- 實驗室中的虛擬機器需要存取大學網路共用上的資料集 (或任何其他檔案)。

某些內部部署網路會透過 [ExpressRoute](../expressroute/expressroute-introduction.md) 或[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)連線到 Azure 虛擬網路。 這些服務必須在 Azure Lab Services 外部設定。 若要深入瞭解如何使用 ExpressRoute 將內部部署網路連線到 Azure，請參閱 [ExpressRoute 概觀](../expressroute/expressroute-introduction.md)。 如需使用虛擬網路閘道進行內部部署連線，閘道、指定的虛擬網路和實驗室帳戶必須全都位於相同的區域中。

> [!NOTE]
> 建立將與實驗室帳戶對等互連的 Azure 虛擬網路時，請務必了解虛擬網路的區域對於教室實驗室的建立位置有何影響。  如需詳細資訊，請參閱系統管理員指南的[區域\位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)一節。

## <a name="configure-at-the-time-of-lab-account-creation"></a>在建立實驗室帳戶時設定

在新的[實驗室帳戶建立](tutorial-setup-lab-account.md)期間，您可以挑選 [進階] 索引標籤上的 [對等虛擬網路] 下拉式清單中顯示的現有虛擬網路。此清單只會顯示位於與實驗室帳戶相同區域中的虛擬網路。 選取的虛擬網路已連線 (對等互連) 至實驗室帳戶之下所建立的實驗室。  在進行此變更之後，在實驗室中建立的所有虛擬機器都可以存取對等互連虛擬網路上的資源。

![選取要對等互連的 VNet](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>位址範圍

您也可選擇為實驗室的虛擬機器提供 [位址範圍]。  只有在針對實驗室啟用**對等虛擬網路**時，才會套用**位址範圍**屬性。 如果已提供位址範圍，實驗室帳戶之下的所有虛擬機器將會建立於該位址範圍內。 此位址範圍應採用 CIDR 標記法 (例如，10.20.0.0/20)，而且不會與任何現有的位址範圍重疊。  在提供位址範圍時，請務必考慮將會建立的「實驗室」數目，並提供位址範圍來容納該數量。 Lab Services 假設每個實驗室最多有 512 部虛擬機器。  例如，具有 '/23' 的 IP 範圍只能建立一個實驗室。  具有 '/21' 的範圍將允許建立四個實驗室。

如果未指定**位址範圍**，則在建立要與您的虛擬網路對等互連的虛擬網路時，Lab Services 會使用 Azure 提供的預設位址範圍。  範圍通常類似於 10.x.0.0/16。  這可能會導致 IP 範圍重疊，因此務必在實驗室設定中指定位址範圍，或檢查要對等互連之虛擬網路的位址範圍。

> [!NOTE]
> 如果實驗室帳戶已對等互連至虛擬網路，但 IP 位址範圍太窄，則實驗室建立可能會失敗。 如果實驗室帳戶中有太多實驗室 (每個實驗室都使用 512 個位址)，則位址範圍中的空間可能不足。 
> 
> 如果實驗室建立失敗，請洽詢您的實驗室帳戶擁有者/管理員，並要求增大位址範圍。 管理員可以使用[為實驗室帳戶中的 VM 指定位址範圍](#specify-an-address-range-for-vms-in-the-lab-account)一節中所述的步驟來增大位址範圍。 

## <a name="configure-after-the-lab-account-is-created"></a>在建立實驗室帳戶後進行設定

如果您並未在建立實驗室帳戶時設定對等網路，則可從 [實驗室帳戶] 頁面的 [實驗室設定] 索引標籤啟用相同的屬性。 對此設定所做的變更，只會套用至變更後所建立的實驗室。 如您在映像中所見，您可以在實驗室帳戶中啟用或停用實驗室的**對等互連虛擬網路**。

![在實驗室建立後啟用或停用 VNet 對等互連](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

當您選取 [對等虛擬網路] 欄位的虛擬網路時，[允許實驗室建立者挑選實驗室位置] 選項已停用。 這是因為實驗室帳戶中的實驗室必須與實驗室帳戶位在相同的區域中，才能與對等虛擬網路中的資源連線。

> [!IMPORTANT]
> 對等互連虛擬網路設定只會套用至變更後所建立的實驗室，而不會0套用至現有的實驗室。


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>為實驗室帳戶中的 VM 指定位址範圍
下列程序中的步驟可為實驗室中的 VM 指定位址範圍。 如果您更新您先前指定的範圍，則修改過的位址範圍只會套用至變更後所建立的 VM。 

當您指定應記住的位址範圍時，以下是一些限制。 

- 前置詞必須小於或等於 23。 
- 如果虛擬網路已對等互連至實驗室帳戶，則提供的位址範圍不能與對等互連虛擬網路中的位址範圍重疊。

1. 在 [實驗室帳戶] 頁面上，選取左側功能表上的 [實驗室設定]。
2. 在 [位址範圍] 欄位中，針對要在實驗室中建立的 VM 指定位址範圍。 位址範圍應使用無類別的網域間路由 (CIDR) 標記法 (範例：10.20.0.0/23)。 實驗室中的虛擬機器將會建立在此位址範圍內。
3. 在工具列上選取 [儲存]。 

    ![設定位址範圍](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將共用映像庫連結至實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者新增為實驗室擁有者](how-to-add-user-lab-owner.md)
- [檢視實驗室的防火牆設定](how-to-configure-firewall-settings.md)
- [設定實驗室的其他設定](how-to-configure-lab-accounts.md)
