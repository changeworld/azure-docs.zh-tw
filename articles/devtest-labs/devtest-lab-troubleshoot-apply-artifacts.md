---
title: 針對 Azure DevTest Labs 中的成品問題進行疑難排解 |Microsoft Docs
description: 瞭解如何針對在 Azure DevTest Labs 虛擬機器中套用成品時所發生的問題進行疑難排解。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483188"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>針對在 Azure DevTest Labs 虛擬機器中套用成品時所發生的問題進行疑難排解
在虛擬機器上套用構件可能會因各種原因而失敗。 本文將引導您瞭解一些可協助您找出可能原因的方法。

如果您在本文的任何時間點需要更多協助，您可以聯絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 AZURE DEVTEST LABS (DTL) 專家。 或者，您可以提出 Azure 支援事件。 移至 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後選取 [取得支援]。   

> [!NOTE]
> 本文適用于 Windows 和非 Windows 虛擬機器。 雖然有一些差異，但會在本文中明確地呼叫它們。

## <a name="quick-troubleshooting-steps"></a>快速疑難排解步驟
檢查 VM 是否正在執行。 DevTest Labs 要求 VM 必須正在執行，且 Microsoft Azure 的 [虛擬機器代理程式 (VM 代理程式) ](../virtual-machines/extensions/agent-windows.md) 已安裝且就緒。

> [!TIP]
> 在 **Azure 入口網站**中，流覽至 vm 的 [ **管理構件** ] 頁面，查看 vm 是否已準備好套用成品。 您會在該頁面的最上方看到一則訊息。 
> 
> 使用 **Azure PowerShell**檢查旗標 **canApplyArtifacts**，只有當您展開取得作業時，才會傳回此旗標。 請參閱下列範例命令：

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

## <a name="ways-to-troubleshoot"></a>疑難排解的方法 
您可以使用下列其中一種方法，針對使用 DevTest Labs 和 Resource Manager 部署模型建立的 Vm 進行疑難排解：

- 如果您需要快速地取得可能造成問題之原因的視覺提示， **Azure 入口網站**很棒。
- **Azure PowerShell** -如果您熟悉 PowerShell 提示字元，請使用 Azure PowerShell Cmdlet 快速查詢 DevTest Labs 資源。

> [!TIP]
> 如需有關如何在 VM 內檢查成品執行的詳細資訊，請參閱在 [實驗室中診斷構件失敗](devtest-lab-troubleshoot-artifact-failure.md)。

## <a name="symptoms-causes-and-potential-resolutions"></a>徵兆、原因和可能的解決方式 

### <a name="artifact-appears-to-stop-responding"></a>成品似乎停止回應

在預先定義的超時期限到期，而且成品標示為 **失敗**之前，成品似乎停止回應。

當成品出現當機時，請先判斷它停滯的位置。 在執行期間，您可以在下列任何一個步驟中封鎖成品：

- 在**初始要求期間**。 DevTest Labs 會建立 Azure Resource Manager 範本，以要求 (CSE) 使用自訂腳本擴充功能。 因此，在幕後，會觸發資源群組部署。 當發生此層級的錯誤時，您會取得有問題之 VM 資源群組的 **活動記錄** 中的詳細資料。  
    - 您可以從實驗室 VM 頁面巡覽列存取活動記錄。 當您選取此專案時，您會看到將成品套用 **至虛擬機器** 的專案 (如果套用成品作業是直接觸發) 或 **新增或修改虛擬機器** (如果套用成品作業是 VM 建立程式) 的一部分。
    - 查看這些專案下的錯誤。 有時，錯誤不會據以標記，因此您必須調查每個專案。
    - 調查每個專案的詳細資料時，請務必檢查 JSON 承載的內容。 您可能會在該檔底部看到錯誤。
- **嘗試執行成品時**。 這可能是因為網路或儲存體問題。 請參閱本文稍後的各節，以取得詳細資料。 這也可能是因為編寫腳本的方式。 例如：
    - PowerShell 腳本有 **必要參數**，但無法傳遞值給它，原因是您允許使用者將其保留為空白，或因為您在 artifactfile.js的定義檔中沒有屬性的預設值。 腳本將會停止回應，因為它正在等待使用者輸入。
    - PowerShell 腳本 **需要使用者輸入** 做為執行的一部分。 腳本必須撰寫成以無訊息模式運作，而不需要任何使用者介入。
- **VM 代理程式需要很長的時間才可供使用**。 當 VM 第一次啟動，或第一次安裝自訂腳本擴充功能來處理套用成品的要求時，VM 可能需要升級 VM 代理程式，或等候 VM 代理程式初始化。 可能有 VM 代理程式所相依的服務需要較長的時間來初始化。 在這種情況下，請參閱 [Azure 虛擬機器代理程式總覽](../virtual-machines/extensions/agent-windows.md) ，以取得進一步的疑難排解。

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>確認構件是否因腳本而停止回應

1. 登入有問題的虛擬機器。
2. 將腳本複製到本機的虛擬機器，或在下的虛擬機器上找到它 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` 。 它是下載成品腳本的位置。
3. 使用提升許可權的命令提示字元，在本機執行腳本，並提供用來引發問題的相同參數值。
4. 判斷腳本是否受任何不需要的行為所影響。 如果是的話，請從公用存放庫中要求成品 (更新) ;或者，如果您的私人存放庫) ，請自行 (修正。

> [!TIP]
> 您可以更正 [公用](https://github.com/Azure/azure-devtestlab) 存放庫中裝載之成品的問題，並提交我們的評論和核准的變更。 請參閱[README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)檔中的**投稿**章節。
> 
> 如需撰寫您自己的構件的詳細資訊，請參閱 [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) 檔。

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>若要確認構件是否因為 VM 代理程式而停止回應：
1. 登入有問題的虛擬機器。
2. 使用檔案總管流覽至 **C:\WindowsAzure\logs**。
3. 找出並開啟檔案 **WaAppAgent**。
4. 尋找 VM 代理程式啟動時顯示的專案，以及何時完成初始化 (也就是第一個) 傳送的信號。 偏好較新的專案，或特別是您遇到問題的時間週期內的專案。

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    在此範例中，您可以看到 VM 代理程式開始時間花費了10分鐘和20秒的時間，因為傳送了一個信號。 這種情況的原因是 OOBE 服務花很長的時間才能啟動。

> [!TIP]
> 如需 Azure 擴充功能的一般資訊，請參閱 [azure 虛擬機器擴充功能和功能](../virtual-machines/extensions/overview.md)。

## <a name="storage-errors"></a>儲存體錯誤
DevTest Labs 需要存取為快取構件建立的實驗室儲存體帳戶。 當 DevTest Labs 套用成品時，它會從已設定的存放庫讀取成品設定和其檔案。 根據預設，DevTest Labs 會設定 **公用**成品存放庫的存取權。

視 VM 的設定方式而定，它可能無法直接存取此存放庫。 因此，根據設計，DevTest Labs 會將構件快取在實驗室首次初始化時所建立的儲存體帳戶中。

如果以任何方式封鎖對此儲存體帳戶的存取，則當流量從 VM 封鎖至 Azure 儲存體服務時，您可能會看到類似下列的錯誤：

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

上述錯誤會出現在 [成品**結果**] 頁面的 [**管理**成品] 下的 [**部署訊息**] 區段中。 它也會出現在虛擬機器的資源群組下的 **活動記錄** 中。

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>為了確保不會封鎖與 Azure 儲存體服務的通訊：

- **檢查是否有新增的網路安全性群組 (NSG) **。 可能是新增了訂用帳戶原則，其中 Nsg 會在所有虛擬網路中自動設定。 它也會影響實驗室的預設虛擬網路（如果使用的話），或您實驗室中設定的其他虛擬網路，以用於建立 Vm。
- **檢查預設實驗室的儲存體帳戶** (也就是建立實驗室時所建立的第一個儲存體帳戶，其名稱通常以字母 "a" 開頭，且以多位數數位結尾，也就是 \<labname\> # ) 。
    1. 流覽至實驗室的資源群組。
    2. 找出 **儲存體帳戶**類型的資源，其名稱符合慣例。
    3. 流覽至名為 [ **防火牆和虛擬網路**] 的儲存體帳戶頁面。
    4. 確定它已設定為 [ **所有網路**]。 如果選取 [ **選取的網路** ] 選項，則請確定已將用來建立 vm 的實驗室虛擬網路新增至清單中。

如需更深入的疑難排解，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](../storage/common/storage-network-security.md)。

> [!TIP]
> **確認網路安全性群組規則**。 使用 [IP 流程驗證](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) 來確認網路安全性群組中的規則會封鎖進出虛擬機器的流量。 您也可以審核有效的安全性群組規則，以確保輸入 **允許** NSG 規則存在。 如需詳細資訊，請參閱 [使用有效安全性規則對 VM 流量流程進行疑難排解](../virtual-network/diagnose-network-traffic-filter-problem.md)。

## <a name="other-sources-of-error"></a>錯誤的其他來源
有其他較不常發生的錯誤來源。 請務必評估每個值，以查看是否適用于您的案例。 以下是其中一項： 

- 私人存放庫的**個人存取權杖已過期**。 過期時，將不會列出成品，而任何從具有到期私用存取權杖的儲存機制參考成品的腳本將會相應地失敗。

## <a name="next-steps"></a>接下來的步驟
如果沒有發生這些錯誤，而您仍然無法套用成品，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
