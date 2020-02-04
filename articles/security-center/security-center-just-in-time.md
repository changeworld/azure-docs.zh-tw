---
title: Azure 資訊安全中心中的 Just-In-Time 虛擬機器存取 | Microsoft Docs
description: 本文件示範 Azure 資訊安全中心的 Just-In-Time VM 存取如何協助您控制 Azure 虛擬機器的存取。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 69a5452134e290f2072a9316ce1f067296ed2320
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939404"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>使用 Just-In-Time 管理虛擬機器存取

即時（JIT）虛擬機器（VM）存取可用於鎖定 Azure Vm 的輸入流量、降低暴露于攻擊的風險，並在需要時輕鬆地連接到 Vm。

> [!NOTE]
> Just-in-Time 功能由資訊安全中心的標準層提供。 若要深入了解資訊安全中心的定價層，請參閱[價格](security-center-pricing.md)。


> [!NOTE]
> 資訊安全中心 Just-In-Time VM 存取目前僅支援透過 Azure Resource Manager 部署的 VM。 若要深入了解傳統部署和資源管理員部署的模型，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="attack-scenario"></a>攻擊案例

暴力密碼破解攻擊通常會以管理連接埠為目標，作為取得 VM 存取權的手段。 如果成功，攻擊者便可以控制 VM，並在您的環境中建立據點。

若要降低暴露於暴力密碼破解攻擊，其中一個方法是限制的連接埠開啟時間。 管理埠不需要在任何時間開啟。 只有在您連線到 VM 時，才需要開啟它們，例如執行管理或維護工作。 當啟用時，資訊安全中心會使用[網路安全性群組](../virtual-network/security-overview.md#security-rules)（NSG）和 Azure 防火牆規則，以限制對管理埠的存取，讓攻擊者無法將其設為目標。

![Just-in-time 案例](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JIT 存取如何運作？

啟用 Just-In-Time 時，資訊安全中心會建立 NSG 規則，藉此鎖定進入 Azure VM 的流量。 系統會鎖定選取的 VM 連接埠的輸入流量。 Just-In-Time 解決方案會控制這些連接埠。

當使用者要求存取 VM 時，資訊安全中心會檢查使用者是否有該 VM 的[角色型存取控制（RBAC）](../role-based-access-control/role-assignments-portal.md)許可權。 如果要求經過核准，資訊安全中心會自動設定網路安全性群組（Nsg）和 Azure 防火牆，以允許輸入流量進入選取的埠，以及要求的來源 IP 位址或範圍（以指定的時間量為限）。 時間到期之後，資訊安全中心會將 NSG 還原為其先前的狀態。 但是，已經建立的連線不會中斷。

 > [!NOTE]
 > 如果已針對 Azure 防火牆後方的 VM 核准 JIT 存取要求，資訊安全中心會自動變更 NSG 和防火牆原則規則。 針對指定的時間長度，規則允許輸入流量進入選取的埠，並要求來源 IP 位址或範圍。 經過一段時間之後，資訊安全中心會將防火牆和 NSG 規則還原成先前的狀態。


## <a name="permissions-needed-to-configure-and-use-jit"></a>設定和使用 JIT 所需的權限

| 若要讓使用者能夠： | 要設定的許可權|
| --- | --- |
| 設定或編輯 VM 的 JIT 原則 | *將這些動作指派給角色：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> 在 VM 的訂用帳戶或資源群組範圍內： <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|要求存取虛擬機器的 JIT 存取 | *將這些動作指派給使用者：*  <ul><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>在與 VM 相關聯的訂用帳戶或資源群組範圍內：<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍內：<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  在訂用帳戶或資源群組或 VM 的範圍內：<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>在 VM 上設定 JIT

有三種方式可在 VM 上設定 JIT 原則：

- [在 Azure 資訊安全中心中設定 JIT 存取](#jit-asc)
- [在 Azure VM 分頁中設定 JIT 存取](#jit-vm)
- [以程式設計方式在 VM 上設定 JIT 原則](#jit-program)

## <a name="configure-jit-in-security-center"></a>在資訊安全中心中設定 JIT

從資訊安全中心，您可以使用 JIT 原則來設定 JIT 原則，並要求存取 VM


### 在資訊安全中心的 VM 上設定 JIT 存取<a name="jit-asc"></a>

1. 開啟 [資訊安全中心] 儀表板。

2. 在左窗格中，選取 [Just-in-Time VM 存取]。

    ![[Just-In-Time VM 存取] 圖格](./media/security-center-just-in-time/just-in-time.png)

    [Just-In-Time VM 存取] 視窗隨即開啟。

      ![啟用 Just-In-Time 存取](./media/security-center-just-in-time/enable-just-in-time.png)

    [Just-In-Time VM 存取] 會提供 VM 狀態的相關資訊：

    - [已設定] - 已設定為支援 Just-In-Time VM 存取的 VM。 其會提供過去一週的資料，包含每個 VM 核准的要求數量、上次存取的日期和時間、以及最後一位使用者。
    - **建議**-可支援即時 vm 存取但尚未設定為的 vm。 建議您啟用這些 VM 的 Just-In-Time VM 存取控制。
    - [不建議] - 可能會導致不建議 VM 進行設定的原因如下：
      - 缺少 NSG - Just-In-Time 解決方案需要 NSG。
      - 傳統 VM - 資訊安全中心 Just-In-Time VM 存取目前僅支援透過 Azure Resource Manager 部署的 VM。 Just-In-Time 解決方案不支援傳統部署。 
      - 其他-如果在訂用帳戶或資源群組的安全性原則中，即時解決方案已關閉，或 VM 缺少公用 IP，且未備妥 NSG，則 VM 會在此類別中。

3. 選取 [建議] 索引標籤。

4. 在 [**虛擬機器**] 下，按一下您想要啟用的 vm。 這會讓 VM 旁邊顯示核取記號。

5. 按一下 [**在 vm 上啟用 JIT**]。
   -. 此刀鋒視窗會顯示 Azure 資訊安全中心建議的預設連接埠：
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. 您也可以設定自訂埠：

      1. 按一下 [新增]。 [**新增埠**設定] 視窗隨即開啟。
      2. 針對您選擇要設定的每個埠（預設和自訂），您可以自訂下列設定：

    - **通訊協定類型** - 核准要求時在此連接埠上允許的通訊協定。
    - **允許的來源 IP 位址** - 核准要求時在此連接埠上允許的 IP 範圍。
    - **要求時間上限** - 可開啟特定連接埠的時間範圍上限。

     3. 按一下 [確定]。

1. 按一下 [檔案]。

> [!NOTE]
>啟用 VM 的 JIT VM 存取時，Azure 資訊安全中心會在與 Azure 防火牆相關聯的網路安全性群組中，為選取的埠建立「拒絕所有輸入流量」規則。 如果已針對選取的埠建立其他規則，則現有的規則會優先于新的「拒絕所有輸入流量」規則。 如果選取的埠上沒有現有的規則，則新的「拒絕所有輸入流量」規則會優先使用網路安全性群組和 Azure 防火牆。


## <a name="request-jit-access-via-security-center"></a>透過資訊安全中心要求 JIT 存取

若要透過資訊安全中心要求存取 VM：

1. 在 [Just-In-Time VM 存取] 下方，選取 [已設定] 索引標籤。

2. 在 [**虛擬機器**] 下，按一下您想要要求存取的 vm。 這會在 VM 旁加上核取記號。


    - [**連接詳細資料**] 欄位中的圖示會指出 NSG 或 FW 上是否已啟用 JIT。 如果兩者都已啟用，則只會顯示防火牆圖示。

    - [連線**詳細資料**] 欄位提供連接 VM 所需的資訊，以及其開啟的埠。

      ![要求 Just-In-Time 存取](./media/security-center-just-in-time/request-just-in-time-access.png)

3. 按一下 [**要求存取**]。 [**要求存取**] 視窗隨即開啟。

      ![JIT 詳細資料](./media/security-center-just-in-time/just-in-time-details.png)

4. 在 [要求存取] 下方，對於每個虛擬機器，設定要開啟的連接埠，以及對連接埠開放的來源 IP 位址和開啟連接埠的時間範圍。 只有在即時原則中設定的埠，才可以要求存取。 每個連接埠都具有衍生自 Just-In-Time 原則的許可時間上限。

5. 按一下 [**開啟埠**]。

> [!NOTE]
> 如果要求存取的使用者位於 Proxy 後方，[我的 IP] 選項可能無法運作。 您可能需要定義組織的完整 IP 位址範圍。

## <a name="edit-a-jit-access-policy-via-security-center"></a>透過資訊安全中心編輯 JIT 存取原則

可以藉由新增和設定一個對 VM 保護的新連接埠，或是變更與受保護的連接埠相關的其他任何設定，來變更該 VM 現有的 Just-In-Time 原則。

若要編輯虛擬機器的現有 Just-In-Time 原則：
1. 在 [設定] 索引標籤的 [虛擬機器] 下方，按一下該虛擬機器列中的三點圖示，選取要新增連接埠的虛擬機器。 

1. 選取 [編輯]。
1. 在 [JIT VM 存取設定] 下方，您可以對於已經保護的連接埠編輯現有設定，也可以新增自訂連接埠。 
  ![JIT VM 存取](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-security-center"></a>資訊安全中心中的審核 JIT 存取活動

您可以使用記錄搜尋來深入了解 VM 活動。 若要檢視記錄：

1. 在 [Just-In-Time VM 存取] 下方，選取 [已設定] 索引標籤。
2. 在 [ **vm**] 下，按一下該 vm 資料列內的三個點，並選取功能表中的 [**活動記錄**]，以選取要查看其相關資訊的 vm。 [**活動記錄**] 隨即開啟。

   ![選取活動記錄](./media/security-center-just-in-time/select-activity-log.png)

   [活動記錄] 可提供篩選過的檢視，列出該 VM 先前的作業，以及時間、日期和訂用帳戶。

選取 [按一下這裡，將所有項目下載為 CSV 格式] 即可下載記錄資訊。

修改篩選準則，然後**按一下 [** 套用] 以建立搜尋和記錄。



## 從 Azure VM 的頁面設定 JIT 存取<a name="jit-vm"></a>

為了方便起見，您可以在資訊安全中心的 VM 頁面中直接使用 JIT 連接到 VM。

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>透過 Azure VM 頁面在 VM 上設定 JIT 存取

若要在所有 VM 上輕鬆地推出 Just-In-Time 存取，您可以將 VM 設定為只允許直接從 VM 進行 Just-In-Time 存取。

1. 從 [ [Azure 入口網站](https://ms.portal.azure.com)] 中，搜尋並選取 [**虛擬機器**]。 
2. 選取您想要限制為即時存取的虛擬機器。
3. 在功能表中 **，選取 [** 設定]。
4. 在 [**即時存取**] 底下，選取 [**啟用即時原則**]。 

這可為使用下列設定的 VM 啟用 Just-In-Time 存取：

- Windows 伺服器：
    - RDP 連接埠 3389
    - 允許存取上限的三個小時
    - 允許的來源 IP 位址設定為任何
- Linux 伺服器：
    - SSH 連接埠 22
    - 允許存取上限的三個小時
    - 允許的來源 IP 位址設定為任何
     
如果 VM 已經啟用 Just-In-Time，當您移至其組態頁面時，您就能夠看到 Just-In-Time 已啟用，而且您可使用此連結在 Azure 資訊安全中心開啟原則，以檢視和變更設定。

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>透過 Azure VM 分頁要求對 VM 進行 JIT 存取

在 Azure 入口網站中，您嘗試連線到虛擬機器時，Azure 會檢查您是否已經在該虛擬機器上設定 Just-In-Time 存取原則。

- 如果您確實已在虛擬機器上設定了 JIT 原則，可以按一下 [要求存取]，好根據為此所設定的 JIT 原則來存取虛擬機器。 

  >![jit 要求](./media/security-center-just-in-time/jit-request.png)

  使用下列預設參數來要求存取：

  - **來源 IP**： ' Any ' （*）（無法變更）
  - **時間範圍**：三小時（無法變更） <!--Isn't this set in the policy-->
  - **埠號碼**Windows/埠22（適用于 Linux）的 RDP 埠3389（可以變更）

    > [!NOTE]
    > 針對受 Azure 防火牆保護的 VM 核准要求之後，資訊安全中心會為使用者提供適當的連線詳細資料（DNAT 資料表的埠對應），以用來連線至 VM。

- 如果您並未在虛擬機器上設定 JIT，則會提示您設定其 JIT 原則。

  ![JIT 提示](./media/security-center-just-in-time/jit-prompt.png)

## 以程式設計方式在 VM 上設定 JIT 原則<a name="jit-program"></a>

您可以透過 REST API 和透過 PowerShell 設定和使用 Just-In-Time。

## <a name="jit-vm-access-via-rest-apis"></a>透過 REST Api 的 JIT VM 存取

Just-In-Time 虛擬機器存取功能可透過 Azure 資訊安全中心 API 使用。 您可以透過此 API 取得已設定 VM 的相關資訊、新增 VM、要求存取 VM，以及更多作業。 請參閱 [JIT 網路存取原則](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)，以深入了解 Just-In-Time REST API。

## <a name="jit-vm-access-via-powershell"></a>透過 PowerShell 存取 JIT VM

若要透過 PowerShell 使用 Just-In-Time 虛擬機器存取解決方案，請使用官方 Azure 安全性中心 PowerShell Cmdlet，尤其是 `Set-AzJitNetworkAccessPolicy`。

下列範例會在特定虛擬機器上設定 Just-In-Time 虛擬機器存取原則，並設定下列項目：

1.  關閉連接埠 22 和 3389。

2.  分別為其設定時間範圍上限 3 小時，讓它們能針對每個核准的要求開啟。
3.  允許要求存取的使用者控制來源 IP 位址，並允許使用者在系統核准 Just-In-Time 存取要求時建立成功的工作階段。

若要完成這項作業，請在 PowerShell 中執行下列命令：

1.  指派一個變數，為虛擬機器保留 Just-In-Time 虛擬機器存取原則：

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  將虛擬機器 Just-In-Time 虛擬機器存取原則插入陣列中：
    
        $JitPolicyArr=@($JitPolicy)

3.  在所選的虛擬機器上設定 Just-In-Time 虛擬機器存取原則：
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>透過 PowerShell 要求存取 VM

在下列範例中，您可以看到對特定虛擬機器發出的 Just-In-Time 虛擬機器存取要求，其中要求連接埠 22 對特定 IP 位址開啟具體的一段時間：

在 PowerShell 中執行下列命令：
1.  設定 VM 要求存取屬性

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  將 VM 存取要求參數插入陣列中：

        $JitPolicyArr=@($JitPolicyVm1)
3.  傳送要求存取 (使用您在步驟 1 中取得的資源識別碼)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

如需詳細資訊，請參閱 PowerShell Cmdlet 文件。

## <a name="next-steps"></a>後續步驟
您已透過本文了解到資訊安全中心中的 Just-In-Time 虛擬機器存取可如何協助您控制 Azure 虛擬機器的存取。

如要深入了解資訊安全中心，請參閱下列主題：

- [設定安全性原則](tutorial-security-policy.md) — 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理安全性建議](security-center-recommendations.md) — 了解建議如何協助保護您的 Azure 資源。
- [安全性健康情況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健康清況。
- [管理與回應安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
- [監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀態。
- [資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
- [Azure 安全性部落格](https://blogs.msdn.microsoft.com/azuresecurity/) — 尋找有關 Azure 安全性與相容性的部落格文章。

