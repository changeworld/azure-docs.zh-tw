---
title: 如何建立具有共用資源的實驗室 |Azure 實驗室服務
description: 瞭解如何建立需要在學生之間共用資源的實驗室。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443361"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>如何在 Azure 實驗室服務中建立具有共用資源的實驗室

有時候在建立教室實驗室時，可能需要在實驗室中的所有學生之間共用一些資源。  例如，您有一個適用于資料庫類別的授權伺服器或 SQL Server。  本文將討論為實驗室啟用共用資源的步驟。  我們也會討論如何限制對該共用資源的存取。

## <a name="architecture"></a>架構

如下圖所示，我們有一個實驗室帳戶和一個實驗室。  實驗室帳戶會具有 vnet 對等互連設定，因此實驗室的虛擬網路會連接到共用資源的網路。  在下圖中，有兩個具有非重迭 IP 範圍的虛擬網路。  這些 IP 範圍只是範例範圍。  另請注意，共用資源虛擬網路與實驗室帳戶位於相同的訂用帳戶中。

![具有共用資源架構的實驗室服務](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>設定共用資源

建立實驗室之前，必須先建立共用資源的虛擬網路。  如需有關如何建立虛擬網路的詳細資訊，請參閱[建立虛擬網路](../virtual-network/quick-create-portal.md)。  規劃虛擬網路範圍，讓它們不會與實驗室電腦的 ip 位址重迭很重要。  如需規劃網路的詳細資訊，請參閱[規劃虛擬網路](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。 在我們的範例中，共用資源位於範圍為 10.2.0.0/16 的虛擬網路中。  如果尚未這麼做，請[建立子網](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)來保存共用資源。  在此範例中，我們使用 10.2.0.0/24 範圍，但您的範圍可能會因您的網路需求而有所不同。

共用的資源可以是在虛擬機器或 Azure 提供的服務上執行的軟體。 共用資源應該可透過私人 IP 位址使用。  藉由讓共用資源僅供私人 IP 使用，您就可以限制對該共用資源的存取。

此圖表也會顯示網路安全性群組（NSG），可用來限制來自學生 VM 的流量。  例如，您可以撰寫一項安全性規則，指出來自學生 VM 的 IP 位址的流量只能存取一個共用資源，而不需要任何其他。  如需如何設定安全性規則的詳細資訊，請參閱[管理網路安全性群組](../virtual-network/manage-network-security-group.md#work-with-security-rules)。 如果您想要將共用資源的存取限制為特定的實驗室，請從[實驗室帳戶的實驗室設定](manage-labs.md#view-labs-in-a-lab-account)取得實驗室的 IP 位址，並將輸入規則設定為只允許來自該 IP 位址的存取權。  別忘了允許埠49152到65535的 IP 位址。  （選擇性）您可以使用 [[虛擬機器集](how-to-set-virtual-machine-passwords.md)區] 頁面來尋找學生 vm 的私人 IP 位址。

如果您的共用資源是執行必要軟體的 Azure 虛擬機器，您可能必須修改虛擬機器的預設防火牆規則。

## <a name="lab-account"></a>實驗室帳戶

若要使用共用資源，實驗室帳戶必須設定為使用[對等互連的虛擬網路](how-to-connect-peer-virtual-network.md)。  在此情況下，我們將會對包含共用資源的虛擬網路進行對等互連。

>[!WARNING]
>將實驗室帳戶對等互連至共用資源虛擬網路**之後**，您必須建立類別的實驗室。  
範本機器

當您的實驗室帳戶對等互連至虛擬網路之後，範本機器現在應該可以存取共用資源。  視存取的共用資源而定，您可能必須更新防火牆規則。
