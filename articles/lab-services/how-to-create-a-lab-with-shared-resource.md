---
title: 如何建立具有共用資源的實驗室 |Azure 實驗室服務
description: 瞭解如何建立需要在學生之間共用資源的實驗室。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: d4bf4127dc163bb5f034e077b84664828374ba87
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647965"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>如何使用 Azure 實驗室服務中的共用資源建立實驗室

有時候在建立教室實驗室時，可能會有一些資源需要在實驗室中的所有學生之間共用。  例如，您有一個資料庫類別的授權伺服器或 SQL Server。  本文將討論啟用實驗室共用資源的步驟。  我們也會討論如何限制對該共用資源的存取。

## <a name="architecture"></a>架構

如下圖所示，我們將會有實驗室帳戶。  實驗室帳戶將會有 vnet 對等互連設定，因此實驗室的虛擬網路會連線到共用資源的網路。  下圖中有兩個具有非重迭 IP 範圍的虛擬網路。  這些 IP 範圍只是範例範圍。  另請注意，共用資源虛擬網路位於與實驗室帳戶相同的訂用帳戶中。

![具有共用資源架構的實驗室服務](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>設定共用資源

建立實驗室之前，必須先建立共用資源的虛擬網路。  如需有關如何建立虛擬網路的詳細資訊，請參閱 [建立虛擬網路](../virtual-network/quick-create-portal.md)。  規劃虛擬網路範圍，使其不會與實驗室電腦的 ip 位址重迭，是很重要的。  如需規劃網路的詳細資訊，請參閱「 [規劃虛擬網路](../virtual-network/virtual-network-vnet-plan-design-arm.md) 」一文。 在我們的範例中，共用資源位於 10.2.0.0/16 範圍的虛擬網路中。  如果尚未這麼做，請 [建立子網](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) 來保存共用資源。  在此範例中，我們使用 10.2.0.0/24 範圍，但您的範圍可能會因您的網路需求而不同。

共用資源可以是在虛擬機器或 Azure 提供的服務上執行的軟體。 共用資源應可透過私人 IP 位址使用。  藉由僅透過私人 IP 提供共用資源，您可以限制對該共用資源的存取。

此圖也會顯示 (NSG) 的網路安全性群組，可用來限制來自學生 VM 的流量。  例如，您可以撰寫安全性規則，以指出來自學生 VM IP 位址的流量只能存取一個共用資源，而不是其他任何專案。  如需如何設定安全性規則的詳細資訊，請參閱 [管理網路安全性群組](../virtual-network/manage-network-security-group.md#work-with-security-rules)。 如果您想要限制對特定實驗室的共用資源的存取權，請從 [實驗室帳戶中的實驗室設定](manage-labs.md#view-labs-in-a-lab-account) 取得實驗室的 IP 位址，並將輸入規則設定為只允許來自該 IP 位址的存取。  別忘了為該 IP 位址允許埠49152到65535。  （選擇性）您可以使用 [ [虛擬機器集](how-to-set-virtual-machine-passwords.md)區] 頁面來尋找學生 vm 的私人 IP 位址。

如果您的共用資源是執行必要軟體的 Azure 虛擬機器，您可能必須修改虛擬機器的預設防火牆規則。

### <a name="tips-for-shared-resources---license-server"></a>共用資源的秘訣-授權伺服器
其中一個較常見的共用資源是授權伺服器，以下是如何成功設定一個的秘訣。
#### <a name="server-region"></a>伺服器區域
授權伺服器必須連線到對等互連至實驗室的虛擬網路，因此授權伺服器必須位於與實驗室帳戶相同的區域中。

#### <a name="static-private-ip-and-mac-address"></a>靜態私人 IP 和 MAC 位址
依預設，虛擬機器有一個動態私人 ip，在 [您設定任何軟體之前，請先將私人 ip 設定為靜態](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。 這會將私人 IP 和 MAC 位址設定為靜態。  

#### <a name="control-access"></a>控制存取權
控制授權伺服器的存取是關鍵的。  VM 設定完成之後，仍需要進行維護、疑難排解和更新的存取。  以下是一些不同的方法。
- [設定及時 (JIT) 在 Azure 資訊安全中心內的存取。](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [設定網路安全性群組以限制存取。](../virtual-network/network-security-groups-overview.md)
- [設定防禦以允許對授權伺服器的安全存取。](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>實驗室帳戶

若要使用共用資源，實驗室帳戶必須設定為使用 [對等互連虛擬網路](how-to-connect-peer-virtual-network.md)。  在此情況下，我們將會對包含共用資源的虛擬網路進行對等互連。

>[!WARNING]
>將實驗室帳戶對等互連至共用資源虛擬網路 **之後** ，您必須建立類別的實驗室。  
範本電腦

當您的實驗室帳戶對等互連至虛擬網路之後，範本電腦現在應該就可以存取共用資源。  您可能必須根據所存取的共用資源，更新防火牆規則。