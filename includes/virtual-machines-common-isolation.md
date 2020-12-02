---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: bd31152c6742271658cf4fd7ac2a5e2957651c84
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96477680"
---
Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。 在硬體世代淘汰時，隔離的大小會在特定硬體世代上運作，並且會被淘汰。

隔離的虛擬機器大小最適合需要與其他客戶工作負載高度隔離的工作負載，原因包括符合規範和法規需求。  使用隔離大小可確保只有您的虛擬機器會在該特定伺服器執行個體上執行。 


此外，當隔離大小的 Vm 很大時，客戶可以選擇使用 [適用于 nested 虛擬機器的 Azure 支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)來細分這些 vm 的資源。

目前的隔離虛擬機器供應項目包括：
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2


> [!NOTE]
> 隔離的 VM 大小具有有限的硬體壽命。 請參閱下方以取得詳細資料

## <a name="deprecation-of-isolated-vm-sizes"></a>取代隔離的 VM 大小

因為隔離的 VM 大小是硬體系結大小，所以 Azure 會在正式淘汰的大小之前12個月提供提醒。  Azure 也會在下一個硬體版本上提供更新的隔離大小，客戶可考慮將其工作負載移至這些版本。

| 大小 | 隔離停用日期 | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 2020 年 5 月 15 日 |
| Standard_D15_v2<sup>1</sup>  | 2020 年 5 月 15 日 |

<sup>1</sup>  如需 Standard_DS15_v2 和 Standard_D15_v2 隔離淘汰計畫的詳細資料，請參閱常見問題


## <a name="faq"></a>常見問題集
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>問：即將淘汰的大小，或只有「隔離」功能？
**答**：如果虛擬機器大小沒有 "i" 注標，則只會淘汰「隔離」功能。 如果不需要隔離，則不需要採取任何動作，且 VM 將繼續如預期般運作。 範例包括 Standard_DS15_v2、Standard_D15_v2、Standard_M128ms 等等。如果虛擬機器大小包含 "i" 注標，則大小將會被淘汰。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>問：當我的 vm 落在非隔離硬體上時，是否會有停機時間？
**答**：如果不需要隔離，就不需要採取任何動作，也不會有停機時間。

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>問：移至非隔離的虛擬機器是否有任何成本差異？
**答**：否

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>問：何時會淘汰其他隔離的大小？
**答**：我們將在正式取代的隔離大小之前12個月提供提醒。

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>問：我是依賴銀級或金級耐久性層的 Azure Service Fabric 客戶。 這種變更對我有何影響？
**答**：否。 即使在這項變更之後，Service Fabric 的 [持久性層](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) 提供的保證仍會繼續運作。 如果基於其他原因需要實體硬體隔離，您可能仍需要採取上述其中一個動作。 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>問： D15_v2 或 DS15_v2 隔離淘汰的里程碑為何？ 
**A**： 
 
| 日期 | 動作 |
|---|---| 
| 2019 年 11 月 18 日 | D/DS15i_v2 (PAYG，1年 RI) 的可用性 | 
| 2020月14日 | 購買 D/DS15i_v2 1 年 RI 的最後一天 | 
| 2020 年 5 月 15 日 | 已移除 D/DS15_v2 隔離保證 | 
| 2021 5 月15日 | 淘汰 D/DS15i_v2 (所有客戶，但在2019年11月18日前購買了3年 RI 的 D/DS15_v2) | 
| 2022年11月17日 | 若已在2019年11月18日之前購買 DS15_v2 了3年 RI 的客戶，請淘汰 D/DS15i_v2 ()  |

## <a name="next-steps"></a>後續步驟

客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。
