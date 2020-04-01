---
title: Azure 資訊安全中心中的 Just-In-Time 虛擬機器存取 | Microsoft Docs
description: 本文件示範 Azure 資訊安全中心的 Just-In-Time VM 存取如何協助您控制 Azure 虛擬機器的存取。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 51985c5fa4b2296e43c0a062d0af84a1bb51e89c
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397763"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>以及時存取保護管理埠

如果您位於安全中心的標準定價層(請參閱[定價](/azure/security-center/security-center-pricing)),則可以使用及時 (JIT) 虛擬機器 (VM) 訪問許可權鎖定 Azure VM 的入站流量。 這減少了受到攻擊的風險,同時提供了在需要時輕鬆連接到 VM 的訪問。

> [!NOTE]
> 資訊安全中心 Just-In-Time VM 存取目前僅支援透過 Azure Resource Manager 部署的 VM。 若要深入了解傳統部署和資源管理員部署的模型，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/management/deployment-models.md)。

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>在 VM 上設定 JIT

在 VM 上設定 JIT 策略有三種方法:

- [在 Azure 安全中心設定 JIT 存取](#jit-asc)
- [在 Azure VM 頁中設定 JIT 存取](#jit-vm)
- [以程式設計方式在 VM 上設定 JIT 政策](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>在 Azure 安全中心設定 JIT

從安全中心,您可以設定 JIT 政策,並使用 JIT 政策請求對 VM 的存取

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>在安全中心的 VM 上設定 JIT 存取權限<a name="jit-asc"></a>

1. 開啟 [資訊安全中心]**** 儀表板。

1. 在左窗格中，選取 [Just-in-Time VM 存取]****。

    ![[Just-In-Time VM 存取] 圖格](./media/security-center-just-in-time/just-in-time.png)

    **「 即時 VM 存**取「視窗將開啟並顯示有關 VM 狀態的資訊:

    - [已設定]**** - 已設定為支援 Just-In-Time VM 存取的 VM。 其會提供過去一週的資料，包含每個 VM 核准的要求數量、上次存取的日期和時間、以及最後一位使用者。
    - **推薦**- 支援即時 VM 存取許可權但尚未設定為的 VM 的 VM。 建議您啟用這些 VM 的 Just-In-Time VM 存取控制。
    - [不建議]**** - 可能會導致不建議 VM 進行設定的原因如下：
      - 缺少 NSG - Just-In-Time 解決方案需要 NSG。
      - 傳統 VM - 資訊安全中心 Just-In-Time VM 存取目前僅支援透過 Azure Resource Manager 部署的 VM。 Just-In-Time 解決方案不支援傳統部署。 
      - 其他 - 如果訂閱或資源組的安全策略中關閉了及時解決方案,或者 VM 缺少公共 IP 並且沒有 NSG,則 VM 屬於此類別。

1. 選取 [建議]**** 索引標籤。

1. 在 **「虛擬電腦」** 下,按一下要啟用的 VM。 這會讓 VM 旁邊顯示核取記號。

      ![啟用 Just-In-Time 存取](./media/security-center-just-in-time/enable-just-in-time.png)

1. 按一下**在 VM 上啟用 JIT。** 將開啟一個窗格,顯示 Azure 安全中心建議的預設連接埠:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. 或,您可以將自訂連接埠加入清單中:

      1. 按一下 **[新增]**。 將打開 **「添加埠設定**」視窗。
      1. 對於選擇設定的每個埠(預設連接埠和自訂連接埠),可以自訂以下設定:
            - **通訊協定類型** - 核准要求時在此連接埠上允許的通訊協定。
            - **允許的來源 IP 位址** - 核准要求時在此連接埠上允許的 IP 範圍。
            - **要求時間上限** - 可開啟特定連接埠的時間範圍上限。

     1. 按一下 [確定]  。

1. 按一下 [儲存]****。

> [!NOTE]
>為 VM 啟用 JIT VM 訪問後,Azure 安全中心會為與之關聯的網路安全組中的選定埠及其 Azure 防火牆創建"拒絕所有入站流量"規則。 如果為所選埠創建了其他規則,則現有規則優先於新的"拒絕所有入站流量"規則。 如果所選埠上沒有現有規則,則新的"拒絕所有入站流量"規則將在網路安全組和 Azure 防火牆中佔據最高優先順序。


## <a name="request-jit-access-via-security-center"></a>透過安全中心要求 JIT 存取

要透過安全中心要求存取 VM:

1. 在 [Just-In-Time VM 存取]**** 下方，選取 [已設定]**** 索引標籤。

1. 在 **「虛擬機」** 下,按一下要請求訪問的 VM。 這會在 VM 旁邊放置一個複選標記。

    - 「**連線詳細資訊」** 列中的圖示指示是否在 NSG 或 FW 上啟用了 JIT。 如果兩者都啟用了,則僅顯示防火牆圖示。

    - "**連線詳細資訊"** 列提供連接 VM 及其打開連接埠所需的資訊。

      ![要求 Just-In-Time 存取](./media/security-center-just-in-time/request-just-in-time-access.png)

1. 按下 **「請求訪問**」。 將打開 **「請求訪問**」視窗。

      ![JIT 詳細資訊](./media/security-center-just-in-time/just-in-time-details.png)

1. 在 [要求存取]**** 下方，對於每個虛擬機器，設定要開啟的連接埠，以及對連接埠開放的來源 IP 位址和開啟連接埠的時間範圍。 只能請求訪問在即時策略中配置的埠。 每個連接埠都具有衍生自 Just-In-Time 原則的許可時間上限。

1. 按下 **「打開埠**」。。

> [!NOTE]
> 如果要求存取的使用者位於 Proxy 後方，[我的 IP]**** 選項可能無法運作。 您可能需要定義組織的完整 IP 位址範圍。



## <a name="edit-a-jit-access-policy-via-security-center"></a>透過安全中心編輯 JIT 存取原則

可以藉由新增和設定一個對 VM 保護的新連接埠，或是變更與受保護的連接埠相關的其他任何設定，來變更該 VM 現有的 Just-In-Time 原則。

若要編輯虛擬機器的現有 Just-In-Time 原則：

1. 在 [設定]**** 索引標籤的 [虛擬機器]**** 下方，按一下該虛擬機器列中的三點圖示，選取要新增連接埠的虛擬機器。 

1. 選取 [編輯]****。

1. 在 [JIT VM 存取設定]**** 下方，您可以對於已經保護的連接埠編輯現有設定，也可以新增自訂連接埠。 
  ![JIT VM 存取](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>稽核安全中心的 JIT 存取活動

您可以使用記錄搜尋來深入了解 VM 活動。 若要檢視記錄：

1. 在 [Just-In-Time VM 存取]**** 下方，選取 [已設定]**** 索引標籤。
2. 在**VM**下,通過單擊該 VM 行中的三個點來查看有關的資訊,並從菜單中選擇 **「活動日誌**」。 活動**日誌**將打開。

   ![選取活動記錄](./media/security-center-just-in-time/select-activity-log.png)

   [活動記錄]**** 可提供篩選過的檢視，列出該 VM 先前的作業，以及時間、日期和訂用帳戶。

選取 [按一下這裡，將所有項目下載為 CSV 格式]**** 即可下載記錄資訊。

修改篩選器,然後單擊 **「應用」** 以建立搜尋和日誌。



## <a name="configure-jit-access-from-an-azure-vms-page"></a>從 Azure VM 的頁面設定 JIT 存取權限<a name="jit-vm"></a>

為方便起見,您可以使用 JIT 直接從安全中心中的 VM 頁面連接到 VM。

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>透過 Azure VM 頁面在 VM 上設定 JIT 存取權限

若要在所有 VM 上輕鬆地推出 Just-In-Time 存取，您可以將 VM 設定為只允許直接從 VM 進行 Just-In-Time 存取。

1. 從[Azure 門戶](https://ms.portal.azure.com)中,搜尋並選擇**虛擬機器**。 
2. 選擇要限制為即時訪問的虛擬機器。
3. 在功能表中,選擇 **「設定**」 。。
4. 在"及時**訪問**"下,選擇 **"及時啟用**"。 

這可為使用下列設定的 VM 啟用 Just-In-Time 存取：

- Windows 伺服器：
    - RDP 連接埠 3389
    - 最多允許存取三小時
    - 允許的源 IP 位址設置為"任意"
- Linux 伺服器：
    - SSH 連接埠 22
    - 最多允許存取三小時
    - 允許的源 IP 位址設置為"任意"
     
如果 VM 已經啟用 Just-In-Time，當您移至其組態頁面時，您就能夠看到 Just-In-Time 已啟用，而且您可使用此連結在 Azure 資訊安全中心開啟原則，以檢視和變更設定。

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>透過 Azure VM 的頁面要求對 VM 的 JIT 存取權限

在 Azure 入口網站中，您嘗試連線到虛擬機器時，Azure 會檢查您是否已經在該虛擬機器上設定 Just-In-Time 存取原則。

- 如果在 VM 上配置了 JIT 策略,則可以按下「**請求存取權限**」,根據為 VM 設置的 JIT 策略授予訪問許可權。 

  >![jit 要求](./media/security-center-just-in-time/jit-request.png)

  使用以下預設參數請求存取:

  - **來源 IP**: "任何'(*) (無法變更)
  - **時間範圍**:三小時(無法變更) <!--Isn't this set in the policy-->
  - **連接埠號**用於 Windows 的 RDP 連接埠 3389 / 適用於 Linux 的連接埠 22(可變更)

    > [!NOTE]
    > 批准受 Azure 防火牆保護的 VM 的請求後,安全中心為使用者提供了用於連接到 VM 的正確連接詳細資訊(DNAT 表的埠映射)。

- 如果未在 VM 上配置 JIT,系統將提示您設定其上的 JIT 策略。

  ![JIT 提示](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>以程式設計方式在 VM 上設定 JIT 政策<a name="jit-program"></a>

您可以透過 REST API 和透過 PowerShell 設定和使用 Just-In-Time。

### <a name="jit-vm-access-via-rest-apis"></a>透過 REST API 進行 JIT VM 存取

Just-In-Time 虛擬機器存取功能可透過 Azure 資訊安全中心 API 使用。 您可以透過此 API 取得已設定 VM 的相關資訊、新增 VM、要求存取 VM，以及更多作業。 請參閱 [JIT 網路存取原則](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)，以深入了解 Just-In-Time REST API。

### <a name="jit-vm-access-via-powershell"></a>透過 PowerShell 進行 JIT VM 存取

若要透過 PowerShell 使用 Just-In-Time 虛擬機器存取解決方案，請使用官方 Azure 安全性中心 PowerShell Cmdlet，尤其是 `Set-AzJitNetworkAccessPolicy`。

下列範例會在特定虛擬機器上設定 Just-In-Time 虛擬機器存取原則，並設定下列項目：

1.    關閉連接埠 22 和 3389。

2.    分別為其設定時間範圍上限 3 小時，讓它們能針對每個核准的要求開啟。
3.    允許要求存取的使用者控制來源 IP 位址，並允許使用者在系統核准 Just-In-Time 存取要求時建立成功的工作階段。

若要完成這項作業，請在 PowerShell 中執行下列命令：

1.    指派一個變數，為虛擬機器保留 Just-In-Time 虛擬機器存取原則：

        $JitPolicy = (*id="/訂閱/訂閱 ID/資源組/資源組/資源組/提供者/微軟.計算/虛擬機/VMNAME"埠=({編號=22;       協定*\";       允許的Source位址首碼\("");*       最大請求訪問持續時間="PT3H"*,[數位=3389;       協定*\";       允許的Source位址首碼\("");*       最大請求存取持續時間=「PT3H」*))

2.    將虛擬機器 Just-In-Time 虛擬機器存取原則插入陣列中：
    
        $JitPolicyArr_($JitPolicy)

3.    在所選的虛擬機器上設定 Just-In-Time 虛擬機器存取原則：
    
        設定-AzJitNetwork 存取策略 - 金德' -位置' -名稱"預設' - 資源群組名稱'資源群組' -虛擬機$JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>透過 PowerShell 要求對 VM 的存取

在下列範例中，您可以看到對特定虛擬機器發出的 Just-In-Time 虛擬機器存取要求，其中要求連接埠 22 對特定 IP 位址開啟具體的一段時間：

在 PowerShell 中執行下列命令：
1.    設定 VM 要求存取屬性

        $JitPolicyVm1 = (*id="/訂閱 ID/資源組/資源組/資源組/供應商/微軟.計算/虛擬機/VMNAME"埠=(*編號=22;     endTimeUtc="2018-09-17T17:00.3658798Z";     允許來源位址前置字元("IPV4ADDRESS")*)
2.    將 VM 存取要求參數插入陣列中：

        $JitPolicyArr=($JitPolicyVm1)
3.    傳送要求存取 (使用您在步驟 1 中取得的資源識別碼)

        啟動-AzJit網路存取策略 - 資源 Id"/訂閱/訂閱 ID/資源組/資源組/資源組/供應商/微軟.安全/位置/位置/jitNetworkAccess策略/預設" -虛擬機器$JitPolicyArr

有關詳細資訊,請參閱[PowerShell cmdlet 文件](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)。


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>自動清理冗餘 JIT 規則 

每當更新 JIT 策略時,都會自動運行清理工具以檢查整個規則集的有效性。 該工具查找策略中的規則與 NSG 中的規則不匹配。 如果清理工具發現不匹配,它會確定原因,並在安全的情況下刪除不再需要的內置規則。 清除程式永遠不會刪除您建立的規則。

當清理程式可能移除內建規則時,範例:

- 當存在兩個定義相同的規則,並且一個規則具有高於另一個規則(這意味著,將永遠不會使用低優先順序規則)
- 當規則描述包含與規則中的目標 IP 不符合的 VM 的名稱時 


## <a name="next-steps"></a>後續步驟

您已透過本文了解到資訊安全中心中的 Just-In-Time 虛擬機器存取可如何協助您控制 Azure 虛擬機器的存取。

如要深入了解資訊安全中心，請參閱下列主題：

- Microsoft 學習模組[使用 Azure 安全中心保護伺服器和 VM 免受暴力攻擊和惡意軟體攻擊](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [設定安全原則](tutorial-security-policy.md)– 瞭解如何為 Azure 訂閱和資源組配置安全原則。
- [管理安全建議](security-center-recommendations.md)– 瞭解建議如何幫助保護 Azure 資源。
- [安全性健康情況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健康清況。