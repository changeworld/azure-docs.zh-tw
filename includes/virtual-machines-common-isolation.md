---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77123198"
---
Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。  這些虛擬機器大小最適合需要與其他客戶高度隔離，且涉及合規性和法規要求等元素的工作負載。  客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。

使用隔離大小可確保只有您的虛擬機器會在該特定伺服器執行個體上執行。  目前的隔離虛擬機器供應項目包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

您可以在[這裡](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)深入瞭解每個可用的隔離大小。

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>在 2020 5 月15日淘汰 D15_v2/DS15_v2 隔離
**2020年2月10日更新：「隔離」淘汰時程表已擴充至5月15日，2020」**

Azure 專用主機現已正式推出，可讓您在單一租使用者實體伺服器上執行貴組織的 Linux 和 Windows 虛擬機器。 我們計畫將隔離的 Azure Vm 完全取代為 Azure 專用主機。 **2020 5 月15日之後，** D15_v2/DS15_v2 Azure vm 將不再與硬體隔離。

## <a name="how-does-this-affect-me"></a>此變更對我造成什麼影響？
在 2020 5 月15日之後，我們將不再針對您的 D15_v2/DS15_v2 Azure 虛擬機器提供隔離保證。 

## <a name="what-actions-should-i-take"></a>我應該採取哪些動作？
如果您不需要硬體隔離，則不需要採取任何動作。 

如果您需要隔離，在 2020 5 月15日之前，您必須：

•將您的工作負載[遷移](https://azure.microsoft.com/blog/introducing-azure-dedicated-host)至 Azure 專用主機。

•[要求存取](https://aka.ms/D15iRequestAccess)D15i_v2 並 DS15I_V2 Azure VM，以取得相同的價格效能。 此選項僅適用于隨用隨付和一年期保留實例案例。    

•將您的工作負載[遷移](https://azure.microsoft.com/blog/resize-virtual-machines/)至另一個 Azure 隔離的虛擬機器。 

如需詳細資訊，請參閱下列內容：

## <a name="timeline"></a>時間軸
| Date | 動作 | 
| --- | --- |
| 2019 年 11 月 18 日 | D/DS15i_v2 的可用性（PAYG，1年 RI） |
| 2020 5 月14日  | 購買的最後一天 D/DS15i_v2 1 年 RI | 
| 2020 5 月15日   | 已移除 D/DS15_v2 隔離保證 | 
| 2021 5 月15日  | 淘汰 D/DS15i_v2 （除了在2019年11月18日前購買3年 RI of D/DS15_v2 的所有客戶）| 
| 2022年11月17日  | 當您完成3年後，淘汰 D/DS15i_v2 （適用于在2019年11月18日前購買3年 RI of D/DS15_v2 的客戶） | 

## <a name="faq"></a>常見問題集
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>問： D/DS15_v2 的大小是否會被淘汰？
**答**：否，只有「隔離」功能即將淘汰。 如果您不需要隔離，則不需要採取任何動作。

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>問： D/DS15i_v2 的大小是否會被淘汰？
**答**：是的，只有在5月15日2021時才可使用大小。 若客戶 DS15_v2 在11月18日前購買了3年的協力廠商 ri，2019到2022年11月17日之前，都可以存取 D/DS15i_v2。

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>問：為什麼我在入口網站中看不到新的 D/DS15i_v2 大小？
**答**：如果您是目前的 d/DS15_v2 客戶，而且想要使用新的 d/DS15i_v2 大小，請填寫此[表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>問：為什麼我看不到新的 D/DS15i_v2 大小的任何配額？
**答**：如果您是目前的 d/DS15_v2 客戶，而且想要使用新的 d/DS15i_v2 大小，請填寫此[表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>問：其他隔離的大小何時會淘汰？
**答**：我們會在官方解除委任大小前12個月提供提醒。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>問：當我的 vm 落在非隔離的硬體上時，是否會有停機時間？
**答**：如果您不需要隔離，則不需要採取任何動作，也不會看到任何停機時間。

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>問：移至非隔離的虛擬機器是否有任何成本變更？
**答**：否 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>問：我已購買1或3年期的保留實例，供 D15_v2 或 Ds15_v2。 如何將折扣套用至我的 VM 使用量？
**答**：2019年11月18日前購買的 RIs 會自動將涵蓋範圍延伸到新的隔離 VM 系列。 

| RI |  實例大小彈性 | 權益資格 |   
| --- | --- | --- |
|   D15_v2  |   Off     |   D15_v2 和 D15i_v2 |    
|   D15_v2  |   另一  |   D15_v2 系列和 D15i_v2 全都會獲得 RI 權益。 |    
|   D14_v2  |   另一  |   D15_v2 系列和 D15i_v2 全都會獲得 RI 權益。 |    
 
同樣地，Dsv2 系列也一樣。
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>問：我想要購買 Dv2 的其他保留實例。 我該選哪一種？
**答**：在2019年11月18日之後購買的所有 RIs 都具有下列行為。 

| RI |  實例大小彈性 | 權益資格 |   
| --- | --- | --- |
| D15_v2 |  Off |   僅 D15_v2  
| D15_v2 |  另一 |    D15_v2 系列將會獲得 RI 權益。 新的 D15i_v2 將無法享有此 RI 類型的 RI 權益。 | 
| D15i_v2 |     Off | 僅 D15i_v2 |  
| D15i_v2 |     另一  | 僅 D15i_v2 | 
 
實例大小彈性不能用來套用至任何其他大小，例如 D2_v2、D4_v2 或 D15_v2。 同樣地，針對 Dsv2 系列。  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>問：我可以為 D15i_v2 和 DS15i_v2 購買新的3年 RI 嗎？
**答**：可惜的是，只有1年的 RI 可供新購買。
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>問：我可以將現有的 D15_v2/DS15_v2 保留實例移至隔離大小的保留實例嗎？
**答**：這是不必要的動作，因為此權益會同時適用于隔離和非隔離的大小。 但 Azure 會支援將現有的 D15_v2/DS15_v2 保留實例變更為 D15i_v2/DS15i_v2。 針對所有其他的 Dv2/Dsv2 保留實例，請使用現有的保留實例，或為隔離的大小購買新的保留實例。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>問：我是依賴銀級或金級耐久性層的 Azure Service Fabric 客戶。 這種變更會對我造成影響嗎？
**答**：否。 即使在這項變更之後，Service Fabric 的[持久性層](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)所提供的保證仍會繼續運作。 如果您因為其他原因而需要實體硬體隔離，您可能還是需要採取上述其中一項動作。 
