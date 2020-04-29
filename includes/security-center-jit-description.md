---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597930"
---
## <a name="attack-scenario"></a>攻擊案例

暴力密碼破解攻擊通常會以管理連接埠為目標，作為取得 VM 存取權的手段。 如果成功，攻擊者便可以控制 VM，並在您的環境中建立據點。

若要降低暴露於暴力密碼破解攻擊，其中一個方法是限制的連接埠開啟時間。 管理埠不需要在任何時間開啟。 只有在您連線到 VM 時，才需要開啟它們，例如執行管理或維護工作。 當啟用時，資訊安全中心會使用[網路安全性群組](../articles/virtual-network/security-overview.md#security-rules)（NSG）和 Azure 防火牆規則，以限制對管理埠的存取，讓攻擊者無法將其設為目標。

![Just-in-time 案例](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 存取如何運作？

啟用 Just-In-Time 時，資訊安全中心會建立 NSG 規則，藉此鎖定進入 Azure VM 的流量。 系統會鎖定選取的 VM 連接埠的輸入流量。 Just-In-Time 解決方案會控制這些連接埠。

當使用者要求存取 VM 時，資訊安全中心會檢查使用者是否有該 VM 的[角色型存取控制（RBAC）](../articles/role-based-access-control/role-assignments-portal.md)許可權。 如果要求經過核准，資訊安全中心會自動設定網路安全性群組（Nsg）和 Azure 防火牆，以允許輸入流量進入選取的埠，以及要求的來源 IP 位址或範圍（以指定的時間量為限）。 時間到期之後，資訊安全中心會將 NSG 還原為其先前的狀態。 但是，已經建立的連線不會中斷。

 > [!NOTE]
 > 如果已針對 Azure 防火牆後方的 VM 核准 JIT 存取要求，資訊安全中心會自動變更 NSG 和防火牆原則規則。 針對指定的時間長度，規則允許輸入流量進入選取的埠，並要求來源 IP 位址或範圍。 經過一段時間之後，資訊安全中心會將防火牆和 NSG 規則還原成先前的狀態。


## <a name="permissions-needed-to-configure-and-use-jit"></a>設定和使用 JIT 所需的權限

| 若要讓使用者能夠： | 要設定的許可權|
| --- | --- |
| 設定或編輯 VM 的 JIT 原則 | *將這些動作指派給角色：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在 VM 的訂用帳戶或資源群組範圍內： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|要求存取虛擬機器的 JIT 存取 | *將這些動作指派給使用者：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍內：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍內：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|