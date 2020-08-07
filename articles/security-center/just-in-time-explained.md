---
title: 瞭解 Azure 資訊安全中心中的即時虛擬機器存取
description: 本檔說明 Azure 資訊安全中心中的即時 VM 存取如何協助您控制 Azure 虛擬機器的存取權
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9c77ed2bf0d764fbbbe24770cc70b3fbeec7f678
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833448"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>瞭解及時 (JIT) VM 存取

此頁面說明 Azure 資訊安全中心的即時 (JIT) VM 存取功能和建議背後的邏輯背後的原則。

若要瞭解如何使用 Azure 入口網站 (資訊安全中心或 Azure 虛擬機器) 或以程式設計方式將 JIT 套用至您的 Vm，請參閱[如何使用 jit 保護您的管理埠](security-center-just-in-time.md)。


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>在虛擬機器上開啟管理埠的風險

威脅執行者會使用開放的管理埠（如 RDP 或 SSH）主動尋找可存取的機器。 您的所有虛擬機器都是攻擊的潛在目標。 當 VM 成功遭到入侵時，它會用來做為進入點，以在您的環境中攻擊進一步的資源。



## <a name="why-jit-vm-access-is-the-solution"></a>為什麼 JIT VM 存取是解決方案 

就像所有的網路安全性防護技術一樣，您的目標應該是要減少受攻擊面。 在此情況下，這表示有較少的開啟埠，特別是管理埠。

您的合法使用者也會使用這些埠，因此不可行讓它們保持關閉。

為了解決這種難題，Azure 資訊安全中心提供了 JIT。 使用 JIT 時，您可以鎖定 Vm 的輸入流量、降低暴露于攻擊的風險，並在需要時輕鬆地連接到 Vm。



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>JIT 如何使用網路安全性群組和 Azure 防火牆操作

當您啟用即時 VM 存取時，您可以選取 VM 上將會封鎖輸入流量的埠。 資訊安全中心可確保[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)中所選埠的「拒絕所有輸入流量」規則， (NSG) 和[Azure 防火牆規則](https://docs.microsoft.com/azure/firewall/rule-processing)。 這些規則會限制對您的 Azure Vm 管理埠的存取，並保護其免于遭受攻擊。 

如果選取的埠已有其他規則，則這些現有的規則會優先于新的「拒絕所有輸入流量」規則。 如果選取的埠上沒有現有的規則，則新的規則會優先使用 NSG 和 Azure 防火牆。

當使用者要求存取 VM 時，資訊安全中心檢查使用者是否具有[azure 角色型存取控制， (該 vm 的 AZURE RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)許可權。 如果要求經過核准，資訊安全中心會設定 Nsg 和 Azure 防火牆，以允許在指定的時間內，從相關的 IP 位址 (或範圍) 的輸入流量進入選取的埠。 時間到期之後，資訊安全中心會將 NSG 還原為其先前的狀態。 已建立的連接不會中斷。

> [!NOTE]
> JIT 不支援由[Azure 防火牆管理員](https://docs.microsoft.com/azure/firewall-manager/overview)所控制的 azure 防火牆所保護的 vm。




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>資訊安全中心如何識別哪些 Vm 應套用 JIT

下圖顯示在決定如何將支援的 Vm 分類時，資訊安全中心適用的邏輯： 

[![即時 (JIT) 虛擬機器 (VM) 邏輯流程](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

當資訊安全中心找到可受益于 JIT 的電腦時，它會將該機器新增至建議的 [**狀況不良資源**] 索引標籤。 

![及時 (JIT) 虛擬機器 (VM) 存取建議](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>常見問題-即時虛擬機器存取的相關問題

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>設定和使用 JIT 需要哪些許可權？

如果您想要建立可以使用 JIT 的自訂角色，則需要下表中的詳細資料。

> [!TIP]
> 若要為需要要求對 VM 進行 JIT 存取的使用者建立最低許可權的角色，並不執行其他 JIT 作業，請使用資訊安全中心 GitHub 社區頁面中的[JitLeastPrivilegedRole 腳本](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)。

| 若要讓使用者能夠： | 要設定的許可權|
| --- | --- |
| 設定或編輯 VM 的 JIT 原則 | *將這些動作指派給角色：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在 VM 的訂用帳戶或資源群組範圍內： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|要求存取虛擬機器的 JIT 存取 | *將這些動作指派給使用者：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍內：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍內：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|讀取 JIT 原則| *將這些動作指派給使用者：*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>後續步驟

此頁面說明_為什麼_應該使用 (JIT) 虛擬機器 (VM) 存取的時機。 

前往操作說明文章，以瞭解如何啟用 JIT 並要求存取已啟用 JIT 的 Vm：

> [!div class="nextstepaction"]
> [如何使用 JIT 保護您的管理埠](security-center-just-in-time.md)
