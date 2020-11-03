---
title: Azure 資訊安全中心中的 Just-In-Time 虛擬機器存取 | Microsoft Docs
description: 本檔會示範 Azure 資訊安全中心中的即時 VM 存取 (JIT) 如何協助您控制 Azure 虛擬機器的存取權。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a01dabbd0a3e9d76caaead544be655b9505030d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289201"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>使用 Just-In-Time 存取來保護管理連接埠

使用 Azure 資訊安全中心的即時 (JIT) 虛擬機器 (VM) 存取功能，鎖定 Azure 虛擬機器的輸入流量。 這可減少暴露于攻擊的風險，同時在您需要連線至 VM 時提供簡單的存取。

如需 JIT 運作方式和基礎邏輯的完整說明，請參閱 [及時說明](just-in-time-explained.md)。

此頁面會教您如何在安全性程式中包含 JIT。 您將學習如何： 

- 在 **您的 vm 上啟用 jit** -您可以使用資訊安全中心、PowerShell 或 REST API 一或多個 vm 的自訂選項來啟用 jit。 或者，您可以從 Azure 虛擬機器啟用 JIT 搭配預設的硬式編碼參數。 啟用時，JIT 會藉由在網路安全性群組中建立規則來鎖定 Azure Vm 的輸入流量。
- **要求存取已啟用 JIT 的 VM** -jit 的目標是要確保即使您的連入流量已鎖定，但在需要時，安全性中心仍提供簡單的存取權來連線至 vm。 您可以從「安全性中心」、「Azure 虛擬機器」、PowerShell 或 REST API 要求存取支援 JIT 的 VM。
- **審核活動** -為了確保您的 vm 受到適當的保護，請在定期安全性檢查中檢查對已啟用 JIT 的 vm 的存取權。   



## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|需要[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|支援的 Vm：|![是 ](./media/icons/yes-icon.png) 透過 Azure Resource Manager 部署的 vm。<br>![沒有 ](./media/icons/no-icon.png) 使用傳統部署模型部署的 vm。 [深入瞭解這些部署模型](../azure-resource-manager/management/deployment-models.md)。<br>![未 ](./media/icons/no-icon.png) 受[Azure 防火牆管理員](../firewall-manager/overview.md)控制的 Azure 防火牆保護 vm|
|必要的角色和權限：|**讀取器** 和 **SecurityReader** 角色可以同時查看 JIT 狀態和參數。<br>若要建立可以使用 JIT 的自訂角色，請參閱 [設定和使用 jit 需要哪些許可權？](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)。<br>若要為需要要求對 VM 進行 JIT 存取的使用者建立最低許可權的角色，並不執行任何其他 JIT 作業，請使用來自「安全性中心」 GitHub 頁面的 [設定 JitLeastPrivilegedRole 腳本](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) 。|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||


## <a name="enable-jit-vm-access"></a>啟用 JIT VM 存取 <a name="jit-configure"></a>

您可以使用 [資訊安全中心] 或以程式設計方式，使用您自己的自訂選項為一或多個 Vm 啟用 JIT VM 存取。 

或者，您可以從 Azure 虛擬機器啟用 JIT 搭配預設的硬式編碼參數。

這些選項中的每一個都是在下面個別的索引標籤上說明。

### <a name="azure-security-center"></a>[**Azure 資訊安全中心**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>從 Azure 資訊安全中心在您的 Vm 上啟用 JIT <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="在 Azure 資訊安全中心中設定 JIT VM 存取":::

您可以從「安全性中心」啟用和設定 JIT VM 存取。

1. 開啟 Azure Defender 儀表板，並從 [advanced protection] 區域中，選取 [ **即時 VM 存取** ]。

    [ **即時 VM 存取** ] 頁面隨即開啟，並將您的 vm 分組為下列索引標籤：

    - 已 **設定** -已設定為支援即時 vm 存取的 vm。 針對每部 VM，[已設定] 索引標籤會顯示：
        - 過去七天內核准的 JIT 要求數目
        - 上次存取日期和時間
        - 已設定連接詳細資料
        - 最後一個使用者
    - **未設定-未** 啟用 jit 但可支援 jit 的 vm。 建議您為這些 Vm 啟用 JIT。
    - **不支援** -未啟用 JIT 的 vm，且不支援此功能。 您的 VM 可能會在此索引標籤中，原因如下：
      - 缺少網路安全性群組 (NSG) -JIT 需要設定 NSG
      - 傳統 VM-JIT 支援透過 Azure Resource Manager 部署的 Vm，而不是「傳統部署」。 [深入瞭解傳統與 Azure Resource Manager 部署模型](../azure-resource-manager/management/deployment-models.md)。
      - 其他-如果在訂用帳戶或資源群組的安全性原則中停用了 JIT 解決方案，則您的 VM 可能會在此索引標籤中。

1. 從 [ **未設定** ] 索引標籤中，將 vm 標示為使用 jit 進行保護，然後選取 [ **在 VM 上啟用 JIT** ]。 

    [JIT VM 存取] 頁面隨即開啟，其中列出了安全性中心建議保護的埠：
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    若要接受預設設定，請選取 [ **儲存** ]。

1. 自訂 JIT 選項：

    - 使用 [ **新增** ] 按鈕新增自訂埠。 
    - 從清單中選取其中一個預設埠，以修改其中一個。

    針對每個埠 (自訂和預設) [ **新增埠** 設定] 窗格提供下列選項：

    - **通訊協定** -核准要求時在此埠上允許的通訊協定
    - **允許的來源 ip** -核准要求時在此埠上允許的 IP 範圍
    - **最大要求時間** -可以開啟特定埠的時間範圍上限

     1. 設定埠安全性以滿足您的需求。

     1. 選取 [確定]。

1. 選取 [儲存]  。



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>使用資訊安全中心在啟用 JIT 的 VM 上編輯 JIT 設定 <a name="jit-modify"></a>

您可以藉由新增和設定要保護該 VM 的新埠，或是變更與已受保護之埠相關的任何其他設定，來修改 VM 的即時設定。

若要編輯 VM 的現有 JIT 規則：

1. 開啟 Azure Defender 儀表板，並從 [advanced protection] 區域選取 [自動調整 **應用** 程式控制]。

1. 在 [ **已設定** ] 索引標籤中，以滑鼠右鍵按一下您要新增埠的 VM，然後選取 [編輯]。 

    ![在 Azure 資訊安全中心中編輯 JIT VM 存取設定](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. 在 [JIT VM 存取設定] 下方，您可以對於已經保護的連接埠編輯現有設定，也可以新增自訂連接埠。

1. 當您完成編輯埠之後，請選取 [ **儲存** ]。
 


### <a name="azure-virtual-machines"></a>[**Azure 虛擬機器**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>從 Azure 虛擬機器在您的 Vm 上啟用 JIT

您可以從 Azure 入口網站的 [Azure 虛擬機器] 頁面，在 VM 上啟用 JIT。

![在 Azure 虛擬機器中設定 JIT VM 存取](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> 如果 VM 已啟用即時功能，當您移至其 [設定] 頁面時，您會看到即時啟用，而且您可以使用此連結在 [安全中心] 開啟 [即時 VM 存取] 頁面，並查看和變更設定。

1. 從 [Azure 入口網站](https://ms.portal.azure.com)中，搜尋並選取 [ **虛擬機器** ]。 

1. 選取您要使用 JIT 保護的虛擬機器。

1. 在功能表中 **，選取 [** 設定]。

1. 在 [ **即時存取** ] 下，選取 [ **立即啟用** ]。 

    這可讓您使用下列預設設定，以即時方式存取 VM：

    - Windows 電腦：
        - RDP 連接埠 3389
        - 最大允許存取的三小時
        - 允許的來源 IP 位址設定為 Any
    - Linux 機器：
        - SSH 連接埠 22
        - 最大允許存取的三小時
        - 允許的來源 IP 位址設定為 Any

1. 若要編輯這些值中的任何一項，或在 JIT 設定中新增更多埠，請使用 Azure 資訊安全中心的即時頁面：

    1. 在 [安全性中心] 功能表中，選取 [ **即時 VM 存取** ]。

    1. 在 [ **已設定** ] 索引標籤中，以滑鼠右鍵按一下您要新增埠的 VM，然後選取 [編輯]。 

        ![在 Azure 資訊安全中心中編輯 JIT VM 存取設定](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. 在 [JIT VM 存取設定] 下方，您可以對於已經保護的連接埠編輯現有設定，也可以新增自訂連接埠。

    1. 當您完成編輯埠之後，請選取 [ **儲存** ]。


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>使用 PowerShell 在您的 VM 上啟用 JIT

若要從 PowerShell 啟用即時 VM 存取，請使用官方 Azure 資訊安全中心 PowerShell Cmdlet `Set-AzJitNetworkAccessPolicy` 。

**範例** -使用下列規則，在特定 vm 上啟用即時 VM 存取：

* 關閉埠22和3389
* 為每個設定設定3小時的最長時間範圍，以便每個核准的要求都能開啟
* 允許要求存取的使用者控制來源 IP 位址
* 允許要求存取的使用者在經過核准的即時存取要求時建立成功的會話

下列 PowerShell 命令會建立此 JIT 設定：

1. 指派一個變數，以保存 VM 的即時 VM 存取規則：

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
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
    ```

1. 將 VM 的即時 VM 存取規則插入陣列中：
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 在選取的 VM 上設定即時 VM 存取規則：
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    使用-Name 參數來指定 VM。 例如，若要為兩個不同的 Vm （VM1 和 VM2）建立 JIT 設定，請使用： ```Set-AzJitNetworkAccessPolicy -Name VM1``` 和 ```Set-AzJitNetworkAccessPolicy -Name VM2``` 。


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>使用 REST API 在您的 Vm 上啟用 JIT

Just-In-Time 虛擬機器存取功能可透過 Azure 資訊安全中心 API 使用。 使用此 API 來取得已設定 Vm 的相關資訊、新增新的 Vm、要求存取 VM 等。 

深入瞭解 [JIT 網路存取原則](/rest/api/securitycenter/jitnetworkaccesspolicies)。


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>要求存取已啟用 JIT 的 VM

您可以) 或以程式設計方式，要求從 Azure 入口網站 (存取已啟用 JIT 的虛擬機器。

這些選項中的每一個都是在下面個別的索引標籤上說明。

### <a name="azure-security-center"></a>[**Azure 資訊安全中心**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>從 Azure 資訊安全中心要求存取支援 JIT 的 VM 

當 VM 啟用 JIT 時，您必須要求存取權才能連線至該 VM。 無論您啟用 JIT 的方式為何，您都可以使用任何支援的方式來要求存取權。

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="從安全性中心要求 JIT 存取":::

1. 從 [ **即時 VM 存取** ] 頁面中，選取 [ **已設定** ] 索引標籤。

1. 標示您想要存取的 Vm。

    - [連線 **詳細資料** ] 欄中的圖示指出網路安全性群組或防火牆上是否已啟用 JIT。 如果兩者都啟用，則只會顯示防火牆圖示。

    - [連線 **詳細資料** ] 欄提供連接 VM 所需的資訊，以及其開啟的埠。

1. 選取 [要求存取]。 [ **要求存取** ] 視窗隨即開啟。

1. 在 [要求存取] 下方，對於每個虛擬機器，設定要開啟的連接埠，以及對連接埠開放的來源 IP 位址和開啟連接埠的時間範圍。 您只可以要求存取設定的埠。 每個埠都有從您所建立之 JIT 設定衍生的最大允許時間。

1. 選取 [開啟連接埠]。

> [!NOTE]
> 如果要求存取的使用者位於 Proxy 後方，[我的 IP] 選項可能無法運作。 您可能需要定義組織的完整 IP 位址範圍。



### <a name="azure-virtual-machines"></a>[**Azure 虛擬機器**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>從 Azure 虛擬機器的 [連線] 頁面要求存取支援 JIT 的 VM

當 VM 啟用 JIT 時，您必須要求存取權才能連線至該 VM。 無論您啟用 JIT 的方式為何，您都可以使用任何支援的方式來要求存取權。

  >![jit 即時要求](./media/security-center-just-in-time/jit-request-vm.png)


若要要求來自 Azure 虛擬機器的存取：

1. 在 Azure 入口網站中，開啟 [虛擬機器] 頁面。

1. 選取您要連接的 VM，然後開啟 [連線 **]** 頁面。

    Azure 會檢查是否已在該 VM 上啟用 JIT。

    - 如果未針對 VM 啟用 JIT，系統將會提示您啟用它。

    - 如果已啟用 JIT，請選取 [ **要求存取** ]，以要求的 IP、時間範圍和針對該 VM 設定的埠傳遞存取要求。

> [!NOTE]
> 針對受 Azure 防火牆保護的 VM 核准要求之後，資訊安全中心會為使用者提供適當的連線詳細資料 (從 DNAT 資料表) 的埠對應，以用來連線至 VM。



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>使用 PowerShell 要求存取已啟用 JIT 的 VM

在下列範例中，您可以看到對特定虛擬機器發出的 Just-In-Time 虛擬機器存取要求，其中要求連接埠 22 對特定 IP 位址開啟具體的一段時間：

在 PowerShell 中執行下列命令：

1. 設定 VM 要求存取屬性：

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. 將 VM 存取要求參數插入陣列中：

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. 傳送要求存取 (使用步驟1中的資源識別碼) 

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

若要深入瞭解，請 [參閱 PowerShell Cmdlet 檔](/powershell/scripting/developer/cmdlet/cmdlet-overview)。



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>使用 REST API 要求存取支援 JIT 的 Vm

Just-In-Time 虛擬機器存取功能可透過 Azure 資訊安全中心 API 使用。 使用此 API 來取得已設定 Vm 的相關資訊、新增新的 Vm、要求存取 VM 等。 

深入瞭解 [JIT 網路存取原則](/rest/api/securitycenter/jitnetworkaccesspolicies)。

---








## <a name="audit-jit-access-activity-in-security-center"></a>在安全性中心內審核 JIT 存取活動

您可以使用記錄搜尋來深入了解 VM 活動。 若要檢視記錄：

1. 從 **即時 VM 存取** ，選取 [ **已設定** ] 索引標籤。

1. 針對您想要進行審核的 VM，開啟資料列結尾的省略號功能表。
 
1. 從功能表選取 [ **活動記錄** ]。

   ![選取即時 JIT 活動記錄](./media/security-center-just-in-time/jit-select-activity-log.png)

   活動記錄會提供該 VM 先前作業的篩選視圖，以及時間、日期和訂用帳戶。

1. 若要下載記錄資訊，請選取 [ **下載為 CSV** ]。








## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定和使用即時 VM 存取。 若要瞭解為何應該使用 JIT，請閱讀概念文章來說明其所防禦的威脅：

> [!div class="nextstepaction"]
> [JIT 說明](just-in-time-explained.md)