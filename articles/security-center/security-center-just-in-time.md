---
title: Azure 資訊安全中心中的 Just-In-Time 虛擬機器存取 | Microsoft Docs
description: 本檔示範 Azure 資訊安全中心中的即時 VM 存取（JIT）如何協助您控制 Azure 虛擬機器的存取。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 78f5a34b64736808ac03771dcd8b5380482ab341
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089743"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>使用及時存取保護您的管理埠

使用 Azure 資訊安全中心的及時（JIT）虛擬機器（VM）存取功能，鎖定 Azure 虛擬機器的輸入流量。 這可減少攻擊的風險，並在您需要連線到 VM 時提供輕鬆的存取。

如需有關 JIT 如何運作和基礎邏輯的完整說明，請參閱[即時說明](just-in-time-explained.md)。

本頁面會教您如何在安全性程式中包含 JIT。 您將學習如何： 

- 在**您的 vm 上啟用 jit** -您可以使用您自己的自訂選項，針對一或多個使用資訊安全中心、PowerShell 或 REST API 的 VM 啟用 jit。 或者，您可以從 Azure 虛擬機器使用預設的硬式編碼參數來啟用 JIT。 啟用時，JIT 會藉由在您的網路安全性群組中建立規則，來鎖定對您 Azure Vm 的輸入流量。
- **要求存取已啟用 JIT 的 VM** -jit 的目標是要確保即使您的輸入流量遭到鎖定，資訊安全中心仍然可以在需要時輕鬆地連線到 vm。 您可以從資訊安全中心、Azure 虛擬機器、PowerShell 或 REST API 要求存取已啟用 JIT 的 VM。
- **審核活動**-為了確保您的 vm 受到適當的保護，請檢查已啟用 JIT 的 vm 存取，以作為定期安全性檢查的一部分。   



## <a name="availability"></a>可用性

- 發行狀態：**公開上市**
- 定價：**標準層**。 [深入瞭解定價](/azure/security-center/security-center-pricing)。
- 必要的角色和許可權：
    - 「**讀取**者」和**SecurityReader**角色都可以同時查看 JIT 狀態和參數。
    - 若要建立可與 JIT 搭配使用的自訂角色，請參閱[設定和使用 jit 所需的許可權？](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)。
- 支援的 Vm： 
    - ✔透過 Azure Resource Manager 部署的 Vm。
    - ✘以傳統部署模型部署的 Vm。 [深入瞭解這些部署模型](../azure-resource-manager/management/deployment-models.md)。
    - ✘受 azure[防火牆管理員](https://docs.microsoft.com/azure/firewall-manager/overview)控制的 azure 防火牆所保護的 vm。
- 雲端： 
    - ✔ 商用雲端
    - ✔ 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)




## <a name="enable-jit-vm-access"></a>啟用 JIT VM 存取<a name="jit-configure"></a>

您可以使用資訊安全中心或以程式設計方式，使用您自己的自訂選項，為一或多個 Vm 啟用 JIT VM 存取。 

或者，您可以從 Azure 虛擬機器使用預設的硬式編碼參數來啟用 JIT。

下列每個選項都會在下方的個別索引標籤中說明。

### <a name="azure-security-center"></a>[**Azure 資訊安全中心**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>從 Azure 資訊安全中心在您的 Vm 上啟用 JIT<a name="jit-asc"></a>

![在 Azure 資訊安全中心中設定 JIT VM 存取](./media/security-center-just-in-time/jit-config-security-center.gif)

從資訊安全中心，您可以啟用和設定 JIT VM 存取。

1. 從資訊安全中心的功能表中，選取 [**即時 VM 存取**]。

    [**即時 VM 存取**] 頁面隨即開啟，並將您的 vm 分組為下列索引標籤：

    - 已**設定**-已設定為支援即時 vm 存取的 vm。 針對每個 VM，[已設定] 索引標籤會顯示：
        - 過去七天內已核准的 JIT 要求數
        - 上次存取日期和時間
        - 已設定連線詳細資料
        - 最後一個使用者
    - **未設定**-沒有啟用 jit 的 vm，但可支援 jit。 我們建議您為這些 Vm 啟用 JIT。
    - **不支援**-未啟用 JIT 且不支援此功能的 vm。 基於下列原因，您的 VM 可能會在此索引標籤中：
      - 缺少網路安全性群組（NSG）-JIT 需要設定 NSG
      - 傳統 VM-JIT 支援透過 Azure Resource Manager 部署的 Vm，而不是「傳統部署」。 [深入瞭解傳統與 Azure Resource Manager 部署模型](../azure-resource-manager/management/deployment-models.md)。
      - 其他-如果在訂用帳戶或資源群組的安全性原則中停用了 JIT 解決方案，則您的 VM 可能會在此索引標籤中。

1. 從 [**未設定**] 索引標籤，將 vm 標示為使用 JIT 保護，然後選取 [**在 VM 上啟用 JIT**]。 

    [JIT VM 存取] 頁面隨即開啟，列出資訊安全中心建議保護的埠：
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    若要接受預設設定，請選取 [**儲存**]。

1. 若要自訂 JIT 選項：

    - 使用 [**新增**] 按鈕新增自訂埠。 
    - 從清單中選取其中一個預設埠來加以修改。

    針對每個埠（自訂和預設），[**新增埠**設定] 窗格會提供下列選項：

    - **通訊協定**-核准要求時在此埠上允許的通訊協定
    - **允許的來源 ip**-核准要求時在此埠上允許的 IP 範圍
    - **要求時間上限**-可以開啟特定埠的時間範圍上限

     1. 設定埠安全性以滿足您的需求。

     1. 選取 [確定]。

1. 選取 [儲存]。



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>使用資訊安全中心在已啟用 JIT 的 VM 上編輯 JIT 設定<a name="jit-modify"></a>

您可以藉由新增和設定要為該 VM 保護的新埠，或變更與已受保護埠相關的任何其他設定，來修改 VM 的即時設定。

若要編輯 VM 的現有 JIT 規則：

1. 從資訊安全中心的功能表中，選取 [**即時 VM 存取**]。

1. 從 [**已設定**] 索引標籤中，以滑鼠右鍵按一下您要新增埠的 VM，然後選取 [編輯]。 

    ![在 Azure 資訊安全中心中編輯 JIT VM 存取設定](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. 在 [JIT VM 存取設定]**** 下方，您可以對於已經保護的連接埠編輯現有設定，也可以新增自訂連接埠。

1. 當您完成編輯埠之後，請選取 [**儲存**]。
 


### <a name="azure-virtual-machines"></a>[**Azure 虛擬機器**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>從 Azure 虛擬機器在您的 Vm 上啟用 JIT

您可以從 Azure 入口網站的 [Azure 虛擬機器] 頁面，在 VM 上啟用 JIT。

![在 Azure 虛擬機器中設定 JIT VM 存取](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> 如果 VM 已啟用「僅限時間」，當您移至其 [設定] 頁面時，您會看到 [即時] 已啟用，而且您可以使用此連結在資訊安全中心中開啟 [即時 VM 存取] 頁面，並查看和變更設定。

1. 從 [ [Azure 入口網站](https://ms.portal.azure.com)] 中，搜尋並選取 [**虛擬機器**]。 

1. 選取您想要使用 JIT 保護的虛擬機器。

1. 在功能表中 **，選取 [** 設定]。

1. 在 [**即時存取**] 底下，選取 [**立即啟用**]。 

    這會使用下列預設設定，針對 VM 進行即時存取：

    - Windows 電腦：
        - RDP 連接埠 3389
        - 允許存取上限的三個小時
        - 允許的來源 IP 位址設定為任何
    - Linux 機器：
        - SSH 連接埠 22
        - 允許存取上限的三個小時
        - 允許的來源 IP 位址設定為任何

1. 若要編輯其中任何一個值，或新增更多埠至您的 JIT 設定，請使用 Azure 資訊安全中心的即時頁面：

    1. 從資訊安全中心的功能表中，選取 [**即時 VM 存取**]。

    1. 從 [**已設定**] 索引標籤中，以滑鼠右鍵按一下您要新增埠的 VM，然後選取 [編輯]。 

        ![在 Azure 資訊安全中心中編輯 JIT VM 存取設定](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. 在 [JIT VM 存取設定]**** 下方，您可以對於已經保護的連接埠編輯現有設定，也可以新增自訂連接埠。

    1. 當您完成編輯埠之後，請選取 [**儲存**]。


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>使用 PowerShell 在您的 Vm 上啟用 JIT

若要從 PowerShell 啟用即時 VM 存取，請使用官方 Azure 資訊安全中心 PowerShell Cmdlet `Set-AzJitNetworkAccessPolicy` 。

**範例**-使用下列規則，在特定 VM 上啟用即時 VM 存取：

* 關閉埠22和3389
* 為每個設定最多3小時的時間範圍，以便每個核准的要求都可以開啟它們
* 允許要求存取的使用者控制來源 IP 位址
* 允許要求存取的使用者在核准的即時存取要求時建立成功的會話

下列 PowerShell 命令會建立此 JIT 設定：

1. 指派一個變數，保留 VM 的即時 VM 存取規則：

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. 將 VM 即時 VM 存取規則插入陣列中：
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 在選取的 VM 上設定即時 VM 存取規則：
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    使用-Name 參數來指定 VM。 例如，若要建立兩個不同 Vm （VM1 和 VM2）的 JIT 設定，請使用： ```Set-AzJitNetworkAccessPolicy -Name VM1``` 和 ```Set-AzJitNetworkAccessPolicy -Name VM2``` 。


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>使用 REST API 在您的 Vm 上啟用 JIT

Just-In-Time 虛擬機器存取功能可透過 Azure 資訊安全中心 API 使用。 使用此 API 來取得已設定 Vm 的相關資訊、新增新的虛擬機器、要求 VM 的存取等等。 

若要深入瞭解，請查看[JIT 網路存取原則](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)。


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>要求存取已啟用 JIT 的 VM

您可以從 Azure 入口網站（在資訊安全中心或 Azure 虛擬機器）中或以程式設計方式，要求存取已啟用 JIT 的 VM。

下列每個選項都會在下方的個別索引標籤中說明。

### <a name="azure-security-center"></a>[**Azure 資訊安全中心**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>從 Azure 資訊安全中心要求存取已啟用 JIT 的 VM 

當 VM 已啟用 JIT 時，您必須要求存取權來連接它。 您可以使用任何支援的方式來要求存取權，不論您如何啟用 JIT。

![從資訊安全中心要求 JIT 存取](./media/security-center-just-in-time/jit-request-security-center.gif)

1. 從 [**即時 VM 存取**] 頁面中，選取 [**已設定**] 索引標籤。

1. 將您想要存取的 Vm 標示為已啟用。

    - [連線**詳細資料**] 欄位中的圖示會指出網路安全性群組或防火牆上是否已啟用 JIT。 如果兩者都已啟用，則只會顯示防火牆圖示。

    - [連線**詳細資料**] 欄位提供連接 VM 所需的資訊，以及其開啟的埠。

1. 選取 [要求存取]****。 [**要求存取**] 視窗隨即開啟。

1. 在 [要求存取]**** 下方，對於每個虛擬機器，設定要開啟的連接埠，以及對連接埠開放的來源 IP 位址和開啟連接埠的時間範圍。 只可以要求存取已設定的埠。 每個埠都有從您建立的 JIT 設定所衍生的最大允許時間。

1. 選取 [開啟連接埠]****。

> [!NOTE]
> 如果要求存取的使用者位於 Proxy 後方，[我的 IP]**** 選項可能無法運作。 您可能需要定義組織的完整 IP 位址範圍。



### <a name="azure-virtual-machines"></a>[**Azure 虛擬機器**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>從 Azure 虛擬機器的 [連線] 頁面要求存取已啟用 JIT 的 VM

當 VM 已啟用 JIT 時，您必須要求存取權來連接它。 您可以使用任何支援的方式來要求存取權，不論您如何啟用 JIT。

  >![jit 即時要求](./media/security-center-just-in-time/jit-request-vm.png)


若要從 Azure 虛擬機器要求存取：

1. 在 [Azure 入口網站中，開啟 [虛擬機器] 頁面。

1. 選取您要連接的 VM，然後開啟 [連線 **]** 頁面。

    Azure 會檢查該 VM 上是否已啟用 JIT。

    - 如果未針對 VM 啟用 JIT，系統會提示您啟用它。

    - 如果已啟用 JIT，請選取 [**要求存取**] 以使用要求的 IP、時間範圍和針對該 VM 設定的埠來傳遞存取要求。

> [!NOTE]
> 針對受 Azure 防火牆保護的 VM 核准要求之後，資訊安全中心會為使用者提供適當的連線詳細資料（DNAT 資料表的埠對應），以用來連線至 VM。



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
        
1. 傳送要求存取權（使用步驟1中的資源識別碼）

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

若要深入瞭解，請[參閱 PowerShell Cmdlet 檔](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)。



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>使用 REST API 要求存取已啟用 JIT 的 Vm

Just-In-Time 虛擬機器存取功能可透過 Azure 資訊安全中心 API 使用。 使用此 API 來取得已設定 Vm 的相關資訊、新增新的虛擬機器、要求 VM 的存取等等。 

若要深入瞭解，請查看[JIT 網路存取原則](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)。

---








## <a name="audit-jit-access-activity-in-security-center"></a>資訊安全中心中的審核 JIT 存取活動

您可以使用記錄搜尋來深入了解 VM 活動。 若要檢視記錄：

1. 從 [**即時 VM 存取**] 中，選取 [**已設定**] 索引標籤。

1. 針對您要 audit 的 VM，開啟資料列結尾的省略號功能表。
 
1. 從功能表中選取 [**活動記錄**]。

   ![選取即時 JIT 活動記錄](./media/security-center-just-in-time/jit-select-activity-log.png)

   活動記錄會針對該 VM 的先前作業，以及時間、日期和訂用帳戶，提供篩選過的視圖。

1. 若要下載記錄資訊，請選取 [**下載為 CSV**]。








## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定和使用即時 VM 存取。 若要瞭解為何應該使用 JIT，請閱讀說明其防禦威脅的概念文章：

> [!div class="nextstepaction"]
> [JIT 說明](just-in-time-explained.md)