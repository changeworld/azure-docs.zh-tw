---
title: 瞭解 Azure 資訊安全中心中的即時虛擬機器存取
description: 本檔說明 Azure 資訊安全中心中的即時 VM 存取如何協助您控制 Azure 虛擬機器的存取權
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: fe358e35f2d68a3e55e9d9bb4ac57a13f42085e3
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629233"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>了解 Just-In-Time VM (JIT) 存取

本頁面說明 Azure 資訊安全中心即時 (JIT) VM 存取功能，以及建議背後的邏輯背後的原則。

若要瞭解如何使用 Azure 入口網站 (安全中心或 Azure 虛擬機器) 或以程式設計方式將 JIT 套用至您的 Vm，請參閱 [如何使用 jit 保護您的管理埠](security-center-just-in-time.md)。


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>在虛擬機器上開啟管理埠的風險

威脅執行者主動利用開啟的管理埠（例如 RDP 或 SSH）來搜尋可存取的機器。 您所有的虛擬機器都是攻擊的潛在目標。 VM 一旦遭到入侵，即會成為進入點，據以進一步攻擊您的環境中其他的資源。



## <a name="why-jit-vm-access-is-the-solution"></a>為何 JIT VM 存取是解決方案 

就像所有網路安全性防護技術一樣，您的目標應該是減少攻擊面。 在此情況下，這表示有較少的開啟埠，特別是管理埠。

您的合法使用者也會使用這些埠，因此將它們保持關閉狀態並不實用。

為了解決此難題，Azure 資訊安全中心提供 JIT。 使用 JIT 時，您可以鎖定 Vm 的輸入流量、降低暴露于攻擊的風險，同時讓您視需要輕鬆存取連線至 Vm。



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>如何使用網路安全性群組和 Azure 防火牆進行 JIT 操作

當您啟用即時 VM 存取時，您可以選取 VM 上將封鎖輸入流量的埠。 在 [網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) 和 [Azure 防火牆規則](../firewall/rule-processing.md)中，「安全性中心」可確保您所選取埠的「拒絕所有輸入流量」規則存在。 這些規則會限制對您 Azure Vm 管理埠的存取，並保護其免于遭受攻擊。 

如果選取的埠已有其他規則，則這些現有的規則會優先于新的「拒絕所有輸入流量」規則。 如果選取的埠上沒有任何現有的規則，則新規則在 NSG 和 Azure 防火牆中會優先採用最高優先順序。

當使用者要求存取 VM 時，安全性中心會檢查使用者是否有 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md) 該 vm 的許可權。 如果要求已核准，則 [安全性中心] 會設定 Nsg 和 Azure 防火牆，以允許從相關 IP 位址 (或範圍) 中所選取埠的輸入流量，以指定的時間長度。 時間到期之後，資訊安全中心會將 NSG 還原為其先前的狀態。 已建立的連接不會中斷。

> [!NOTE]
> JIT 不支援由 [Azure 防火牆管理員](../firewall-manager/overview.md)所控制的 Azure 防火牆所保護的 vm。




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>安全性中心如何識別應套用 JIT 的 Vm

下圖顯示當您決定如何將支援的 Vm 分類時，會套用安全中心的邏輯： 

[![及時 (JIT) 虛擬機器 (VM) 邏輯流程](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

當安全性中心找到可從 JIT 受益的電腦時，它會將該電腦新增至 [建議的 **狀況不良資源** ] 索引標籤。 

![及時 (JIT) 虛擬機器 (VM) 存取建議](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>常見問題-即時虛擬機器存取的相關問題

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>設定和使用 JIT 需要哪些許可權？

JIT 要求在訂用帳戶上啟用 [Azure Defender 的伺服器](defender-for-servers-introduction.md) 。 

**讀取器** 和 **SecurityReader** 角色可以同時查看 JIT 狀態和參數。

如果您想要建立可以使用 JIT 的自訂角色，則需要下表中的詳細資料。

> [!TIP]
> 若要為需要要求對 VM 進行 JIT 存取的使用者建立最低許可權的角色，並不執行任何其他 JIT 作業，請使用來自「安全性中心」 GitHub 頁面的 [設定 JitLeastPrivilegedRole 腳本](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) 。

| 若要讓使用者： | 要設定的許可權|
| --- | --- |
| 設定或編輯 VM 的 JIT 原則 | *將這些動作指派給角色：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍中：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在 VM 的訂用帳戶或資源群組範圍中： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|要求存取虛擬機器的 JIT 存取 | *將這些動作指派給使用者：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍中：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在與 VM 相關聯的訂用帳戶或資源群組範圍中：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍中：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍中：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|讀取 JIT 原則| *將這些動作指派給使用者：*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>後續步驟

本頁面說明 _為什麼_ 應該使用 (JIT) 虛擬機器 (VM) 存取的時間。 

前進到操作說明文章，以瞭解如何啟用 JIT 和要求存取支援 JIT 的 Vm：

> [!div class="nextstepaction"]
> [如何使用 JIT 保護您的管理埠](security-center-just-in-time.md)