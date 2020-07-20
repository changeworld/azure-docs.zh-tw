---
title: 針對 Azure DevTest Labs 中成品的問題進行疑難排解 |Microsoft Docs
description: 瞭解如何針對在 Azure DevTest Labs 虛擬機器中套用成品時所發生的問題進行疑難排解。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483188"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>針對在 Azure DevTest Labs 虛擬機器中套用成品時的問題進行疑難排解
在虛擬機器上套用成品可能會因各種原因而失敗。 本文會引導您完成一些方法，以協助找出可能的原因。

如果您在本文中有任何需要協助的地方，您可以與[MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 AZURE DEVTEST LABS （DTL）專家聯繫。 或者，您可以提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。   

> [!NOTE]
> 本文適用于 Windows 和非 Windows 虛擬機器。 雖然有一些差異，但在本文中將會明確地加以呼叫。

## <a name="quick-troubleshooting-steps"></a>快速疑難排解步驟
檢查 VM 是否正在執行。 DevTest Labs 需要 VM 正在執行，且[Microsoft Azure 的虛擬機器代理程式（VM 代理程式）](../virtual-machines/extensions/agent-windows.md)已安裝且已就緒。

> [!TIP]
> 在 [ **Azure 入口網站**中，流覽至 vm 的 [**管理構件**] 頁面，以查看 vm 是否已準備好套用成品。 您會在該頁面的最上方看到一則訊息。 
> 
> 使用**Azure PowerShell**檢查旗標**canApplyArtifacts**，只有在您展開 [取得] 作業時才會傳回。 請參閱下列範例命令：

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>疑難排解方式 
您可以使用下列其中一種方法，針對使用 DevTest Labs 和 Resource Manager 部署模型建立的 Vm 進行疑難排解：

- **Azure 入口網站**-如果您需要快速取得可能造成此問題之原因的視覺提示，這是很棒的。
- **Azure PowerShell** -如果您很熟悉 PowerShell 提示字元，請使用 Azure PowerShell Cmdlet 快速地查詢 DevTest Labs 資源。

> [!TIP]
> 如需有關如何在 VM 中審查成品執行的詳細資訊，請參閱[診斷實驗室中的成品失敗](devtest-lab-troubleshoot-artifact-failure.md)。

## <a name="symptoms-causes-and-potential-resolutions"></a>徵兆、原因和可能的解決方式 

### <a name="artifact-appears-to-stop-responding"></a>成品似乎停止回應

成品似乎停止回應，直到預先定義的超時時間到期，且成品標示為**失敗**為止。

當成品出現停止回應時，請先判斷它停滯的位置。 在執行期間，可以在下列任何一個步驟封鎖成品：

- 在**初始要求期間**。 DevTest Labs 會建立 Azure Resource Manager 範本，要求使用自訂腳本擴充功能（CSE）。 因此，在幕後，會觸發資源群組部署。 發生此層級的錯誤時，您可以在有問題的 VM 的資源群組的**活動記錄**中取得詳細資料。  
    - 您可以從 [實驗室 VM] 頁面導覽列存取 [活動記錄]。 當您選取它時，您會看到將成品套用**至虛擬機器**的專案（如果直接觸發 [套用成品] 作業）或 [**新增或修改虛擬機器**] （如果 [套用成品] 作業是 VM 建立程式的一部分）。
    - 在這些專案下尋找錯誤。 有時候，錯誤不會加上標籤，而且您必須調查每個專案。
    - 調查每個專案的詳細資料時，請務必檢查 JSON 裝載的內容。 您可能會在該檔底部看到錯誤。
- **嘗試執行成品時**。 這可能是因為網路或存放裝置問題所導致。 如需詳細資訊，請參閱本文稍後的個別章節。 這也可能是因為撰寫腳本的方式所造成。 例如：
    - PowerShell 腳本具有**強制參數**，但如果您允許使用者將值保留空白，或因為您在 artifactfile.js的定義檔中沒有屬性的預設值，則可能會有一個錯誤傳遞給它。 腳本會停止回應，因為它正在等候使用者輸入。
    - PowerShell 腳本**需要使用者輸入**做為執行的一部分。 腳本必須撰寫成以無訊息模式工作，而不需要使用者介入。
- **VM 代理程式需要很長的時間才會就緒**。 當 VM 第一次啟動，或第一次安裝自訂腳本擴充功能以提供套用成品的要求時，VM 可能需要升級 VM 代理程式或等待 VM 代理程式初始化。 VM 代理程式所依存的服務可能會花很長的時間來初始化。 在這種情況下，請參閱[Azure 虛擬機器代理程式總覽](../virtual-machines/extensions/agent-windows.md)以取得進一步的疑難排解。

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>若要確認成品是否似乎因腳本而停止回應

1. 登入有問題的虛擬機器。
2. 將腳本複製到虛擬機器的本機，或在下的虛擬機器上找到它 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` 。 這是用來下載成品腳本的位置。
3. 使用提高許可權的命令提示字元，在本機執行腳本，並提供用來造成問題的相同參數值。
4. 判斷腳本是否有任何不必要的行為。 若是如此，請要求更新成品（如果它來自公用存放庫）;或者，自行進行更正（如果是來自您的私人存放庫）。

> [!TIP]
> 您可以更正[公用](https://github.com/Azure/azure-devtestlab)存放庫中裝載之成品的問題，並提交變更以供我們的審查和核准。 請參閱[README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)檔中的**貢獻**一節。
> 
> 如需撰寫您自己成品的詳細資訊，請參閱[AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md)檔。

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>若要確認成品是否似乎因 VM 代理程式而停止回應：
1. 登入有問題的虛擬機器。
2. 使用 File Explorer 流覽至**C:\WindowsAzure\logs**。
3. 找出並開啟 [檔案**WaAppAgent**]。
4. 尋找在 VM 代理程式啟動時顯示的專案，以及何時完成初始化（也就是傳送第一個信號）。 偏好較新的專案，或特別是在您遇到此問題的時段內。

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    在此範例中，您可以看到 VM 代理程式啟動時間花了10分鐘20秒，因為已傳送了一個信號。 這種情況的原因是 OOBE 服務花很長的時間才能啟動。

> [!TIP]
> 如需 Azure 延伸模組的一般資訊，請參閱[azure 虛擬機器擴充功能和功能](../virtual-machines/extensions/overview.md)。

## <a name="storage-errors"></a>儲存體錯誤
DevTest Labs 需要存取建立來快取成品的實驗室儲存體帳戶。 當 DevTest Labs 套用成品時，它會從已設定的存放庫讀取成品設定和其檔案。 根據預設，DevTest Labs 會設定**公用**成品存放庫的存取權。

視 VM 的設定方式而定，它可能無法直接存取此存放庫。 因此，根據設計，DevTest Labs 會在第一次初始化實驗室時所建立的儲存體帳戶中快取構件。

如果以任何方式封鎖此儲存體帳戶的存取，則當流量從 VM 封鎖到 Azure 儲存體服務時，您可能會看到類似下列的錯誤：

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

上述錯誤會出現在 [管理成品] 底下的 [**構件****結果**] 頁面的 [**部署訊息**] 區段中。 它也會出現在有問題之虛擬機器資源群組下的**活動記錄**中。

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>若要確保與 Azure 儲存體服務的通訊不會遭到封鎖：

- **檢查是否有新增的網路安全性群組（NSG）**。 可能是新增了訂用帳戶原則，其中的 Nsg 會在所有虛擬網路中自動設定。 它也會影響實驗室的預設虛擬網路（如果已使用）或您實驗室中設定的其他虛擬網路，以用於建立 Vm。
- **檢查預設實驗室的儲存體帳戶**（也就是建立實驗室時所建立的第一個儲存體帳戶，其名稱通常以字母 "a" 開頭，並以多個數位為結尾，也就是 \<labname\> #）。
    1. 流覽至實驗室的資源群組。
    2. 找出**儲存體帳戶**類型的資源，其名稱符合慣例。
    3. 流覽至名為 [**防火牆和虛擬網路**] 的 [儲存體帳戶] 頁面。
    4. 確定它已設定為 [**所有網路**]。 如果已選取 [**選取的網路**] 選項，則請確定用來建立 vm 的實驗室虛擬網路已新增至清單。

如需更深入的疑難排解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../storage/common/storage-network-security.md)。

> [!TIP]
> **確認網路安全性群組規則**。 使用[IP 流量驗證](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify)來確認網路安全性群組中的規則會封鎖進出虛擬機器的流量。 您也可以檢查有效的安全性群組規則，以確保輸入**允許**NSG 規則存在。 如需詳細資訊，請參閱[使用有效安全性規則對 VM 流量流程進行疑難排解](../virtual-network/diagnose-network-traffic-filter-problem.md)。

## <a name="other-sources-of-error"></a>其他錯誤來源
有其他較不常發生的錯誤來源。 請務必評估每個，以查看其是否適用于您的案例。 以下是其中一項： 

- 私人存放庫的**個人存取權杖已過期**。 當過期時，將不會列出成品，而任何參考具有過期私用存取權杖之存放庫之成品的腳本也會失敗。

## <a name="next-steps"></a>後續步驟
如果這些錯誤皆未發生，而您仍然無法套用成品，您可以將 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
