---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597930"
---
## <a name="attack-scenario"></a>攻擊案例

暴力密碼破解攻擊通常會以管理連接埠為目標，作為取得 VM 存取權的手段。 如果成功，攻擊者便可以控制 VM，並在您的環境中建立據點。

若要降低暴露於暴力密碼破解攻擊，其中一個方法是限制的連接埠開啟時間。 管理埠不需要隨時打開。 它們只需在連接到 VM 時打開，例如執行管理或維護任務。 啟用即時功能後，安全中心使用[網路安全性群組](../articles/virtual-network/security-overview.md#security-rules)（NSG） 和 Azure 防火牆規則，這些規則限制對管理埠的訪問，以便攻擊者無法將其作為攻擊目標。

![Just-in-time 案例](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 存取如何運作？

啟用 Just-In-Time 時，資訊安全中心會建立 NSG 規則，藉此鎖定進入 Azure VM 的流量。 系統會鎖定選取的 VM 連接埠的輸入流量。 Just-In-Time 解決方案會控制這些連接埠。

當使用者請求訪問 VM 時，安全中心會檢查該使用者具有該 VM[的基於角色的存取控制 （RBAC）](../articles/role-based-access-control/role-assignments-portal.md)許可權。 如果請求獲得批准，安全中心將自動設定網路安全性群組 （NSG） 和 Azure 防火牆，以允許入站流量到所選埠和請求的源 IP 位址或範圍，以指定的時間量。 時間到期之後，資訊安全中心會將 NSG 還原為其先前的狀態。 但是，已經建立的連線不會中斷。

 > [!NOTE]
 > 如果為 Azure 防火牆後面的 VM 批准了 JIT 訪問請求，則安全中心會自動更改 NSG 和防火牆策略規則。 對於指定的時間量，規則允許入站流量到所選埠和請求的源 IP 位址或範圍。 時間結束後，安全中心將防火牆和 NSG 規則還原到其以前的狀態。


## <a name="permissions-needed-to-configure-and-use-jit"></a>設定和使用 JIT 所需的權限

| 使使用者能夠： | 要設置的許可權|
| --- | --- |
| 為 VM 配置或編輯 JIT 策略 | *將這些動作指派給角色：*  <ul><li>在與 VM 關聯的訂閱或資源組的範圍內：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在 VM 的訂閱或資源組範圍內： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|要求存取虛擬機器的 JIT 存取 | *將這些動作指派給使用者：*  <ul><li>在與 VM 關聯的訂閱或資源組的範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在與 VM 關聯的訂閱或資源組的範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在訂閱或資源組或 VM 的範圍內：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在訂閱或資源組或 VM 的範圍內：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|