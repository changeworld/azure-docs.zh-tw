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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
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

你可以[在這裡](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)瞭解更多關於每個可用的獨立大小。

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>2020年5月15日退休D15_v2/DS15_v2隔離
**2020 年 2 月 10 日更新："隔離"退休時程表已延長至 2020 年 5 月 15 日"**

Azure 專用主機現在是 GA，它允許您在單租戶物理伺服器上運行組織的 Linux 和 Windows 虛擬機器。 我們計畫用 Azure 專用主機完全替換隔離的 Azure VM。 **2020 年 5 月 15**日之後，D15_v2/DS15_v2 Azure VM 將不再與硬體隔離。

## <a name="how-does-this-affect-me"></a>此變更對我造成什麼影響？
2020 年 5 月 15 日之後，我們將不再為D15_v2/DS15_v2 Azure 虛擬機器提供隔離保證。 

## <a name="what-actions-should-i-take"></a>我應該採取什麼行動？
如果不需要硬體隔離，則無需執行任何操作。 

如果您在 2020 年 5 月 15 日之前需要隔離，則需要：

• 將工作負荷[遷移到](https://azure.microsoft.com/blog/introducing-azure-dedicated-host)Azure 專用主機。

•[請求訪問](https://aka.ms/D15iRequestAccess)D15i_v2並DS15i_v2 Azure VM，以獲得相同的性價比。 此選項僅適用于即用即付和一年預留實例方案。    

• 將工作負荷[遷移到](https://azure.microsoft.com/blog/resize-virtual-machines/)另一個 Azure 隔離虛擬機器。 

有關詳細資訊，請參閱以下內容：

## <a name="timeline"></a>時間軸
| Date | 動作 | 
| --- | --- |
| 2019 年 11 月 18 日 | D/DS15i_v2（PAYG，1 年 RI） |
| 2020年5月14日  | 最後一天購買D/DS15i_v21年RI | 
| 2020年5月15日   | D/DS15_v2隔離保證已移除 | 
| 2021年5月15日  | 停用 D/DS15i_v2（除在 2019 年 11 月 18 日之前購買 3 年 D/DS15_v2 RI 的客戶外）| 
| 2022年11月17日  | 3 年期 RI 完成後停用 D/DS15i_v2（適用于在 2019 年 11 月 18 日之前購買 3 年 D/DS15_v2 RI 的客戶） | 

## <a name="faq"></a>常見問題集
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>問：D/DS15_v2號是否即將停用？
**答**：不，只有"隔離"功能才會停用。 如果不需要隔離，則不需要執行任何操作。

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>問：D/DS15i_v2號會退休嗎？
**答**：是的，尺寸有效期至2021年5月15日。 對於在 2019 年 11 月 18 日之前在 D/DS15_v2 上購買 3 年期 RIs 的客戶，在 2022 年 11 月 17 日之前可以訪問 D/DS15i_v2。

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>問：為什麼我在門戶中看不到新的 D/DS15i_v2 大小？
**A**： 如果您是當前 D/DS15_v2 客戶，並且想要使用新的 D/DS15i_v2 大小，請填寫此[表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>問：為什麼我看不到任何新的 D/DS15i_v2 尺寸配額？
**A**： 如果您是當前 D/DS15_v2 客戶，並且想要使用新的 D/DS15i_v2 大小，請填寫此[表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>問：其他孤立的尺寸何時會停用？
**答**：我們將在正式退役前12個月提供提醒。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>問：當我的 vm 落在非隔離硬體上時，是否有停機時間？
**答**：如果您不需要隔離，則不需要執行任何操作，也不會看到任何停機時間。

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>問：遷移到非隔離虛擬機器是否有任何成本變化？
**A**： 否 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>問：我已經購買了 1 年或 3 年的預留實例，用於D15_v2或Ds15_v2。 折扣將如何應用於我的 VM 使用方式？
**答**：2019 年 11 月 18 日之前購買的 RI 將自動擴展到新的隔離 VM 系列。 

| RI |  實例大小靈活性 | 福利資格 |   
| --- | --- | --- |
|   D15_v2  |   關閉     |   D15_v2和D15i_v2 |    
|   D15_v2  |   另一  |   D15_v2系列和D15i_v2都將獲得 RI 權益。 |    
|   D14_v2  |   另一  |   D15_v2系列和D15i_v2都將獲得 RI 權益。 |    
 
同樣對於 Dsv2 系列。
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>問：我想為 Dv2 購買其他預留實例。 我該選哪一種？
**答**：2019 年 11 月 18 日之後購買的所有 RI 都有以下行為。 

| RI |  實例大小靈活性 | 福利資格 |   
| --- | --- | --- |
| D15_v2 |  關閉 |   僅D15_v2  
| D15_v2 |  另一 |    D15_v2系列將獲得 RI 權益。 新D15i_v2將不符合此 RI 類型的 RI 權益。 | 
| D15i_v2 |     關閉 | 僅D15i_v2 |  
| D15i_v2 |     另一  | 僅D15i_v2 | 
 
實例大小靈活性不能用於應用於任何其他大小，如D2_v2、D4_v2或D15_v2。 同樣，對於 Dsv2 系列。  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>問：我可以為D15i_v2購買新的 3 年 RI 並DS15i_v2嗎？
**答**：不幸的是，沒有，只有1年RI可用於新購買。
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>問：我可以將現有D15_v2/DS15_v2預留實例移動到隔離大小預留實例嗎？
**答**：此操作是不必要的，因為該好處將同時適用于隔離大小和非隔離大小。 但是，Azure 將支援將現有D15_v2/DS15_v2保留實例更改為D15i_v2/DS15i_v2。 對於所有其他 Dv2/Dsv2 預留實例，請使用現有的預留實例或購買隔離大小的新預留實例。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>問：我是依靠銀或金耐久等級的 Azure 服務結構客戶。 這種變化對我有影響嗎？
**答**：不。 即使在此更改之後，Service Fabric[的耐用性層](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)提供的保證仍將繼續發揮作用。 如果出於其他原因需要物理硬體隔離，則可能仍需要執行上述操作之一。 
