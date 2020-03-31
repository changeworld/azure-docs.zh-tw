---
title: 在 Azure 開發人員測試實驗室中排除專案問題 |微軟文檔
description: 瞭解如何解決在 Azure DevTest Labs 虛擬機器中應用專案時出現的問題。
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456985"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>在 Azure 開發人員測試實驗室虛擬機器中應用專案時排除問題
由於各種原因，在虛擬機器上應用工件可能會失敗。 本文將指導您完成一些説明確定可能原因的方法。

如果本文中的任何一點都需要更多説明，則可以在[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上聯繫 Azure 開發人員測試實驗室 （DTL） 專家。 或者，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇"獲取支援"。   

> [!NOTE]
> 本文適用于 Windows 和非 Windows 虛擬機器。 雖然存在一些差異，但本文將明確調用它們。

## <a name="quick-troubleshooting-steps"></a>快速疑難排解步驟
檢查 VM 是否正在運行。 DevTest 實驗室要求 VM 運行，並且[Microsoft Azure 虛擬機器代理 （VM 代理）](../virtual-machines/extensions/agent-windows.md)已安裝完畢並準備就緒。

> [!TIP]
> 在**Azure 門戶**中，導航到 VM 的 **"管理專案"** 頁，以查看 VM 是否已準備好應用專案。 您看到該頁面頂部有一條消息。 
> 
> 使用**Azure PowerShell**，檢查標誌**可以應用偽影**，僅在展開 GET 操作時返回該標誌。 請參閱以下示例命令：

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

## <a name="ways-to-troubleshoot"></a>故障排除方法 
您可以使用以下方法之一對使用 DevTest Labs 和資源管理器部署模型創建的 VM 進行故障排除：

- **Azure 門戶**- 如果需要快速獲得可能導致問題的原因的可視提示，則該門戶非常棒。
- **Azure PowerShell** - 如果您對 PowerShell 提示符感到滿意，請使用 Azure PowerShell Cmdlet 快速查詢開發人員測試實驗室資源。

> [!TIP]
> 有關如何查看 VM 中的專案執行的詳細資訊，請參閱[在實驗室中診斷專案失敗](devtest-lab-troubleshoot-artifact-failure.md)。

## <a name="symptoms-causes-and-potential-resolutions"></a>症狀、原因和潛在解決方法 

### <a name="artifact-appears-to-hang"></a>專案似乎掛起   
專案似乎掛起，直到預定義的超時期限過期，並且專案標記為**失敗**。

當一個工件顯示為掛起時，首先確定它的位置。 在執行過程中，可以在以下任一步驟中阻止專案：

- **在初始請求期間**。 開發人員測試實驗室創建 Azure 資源管理器範本以請求使用自訂腳本擴展 （CSE）。 因此，在後臺，將觸發資源組部署。 發生此級別錯誤時，您將在相關 VM 的資源組**的活動日誌**中獲取詳細資訊。  
    - 可以從實驗室 VM 頁面巡覽列訪問活動日誌。 選擇它時，您將看到一個條目，用於**將工件應用於虛擬機器**（如果應用工件操作是直接觸發的）或**添加或修改虛擬機器**（如果應用工件操作是 VM 創建過程的一部分）。
    - 查找這些條目下的錯誤。 有時，錯誤不會相應地標記，您必須調查每個條目。
    - 在調查每個條目的詳細資訊時，請確保查看 JSON 負載的內容。 您可能會在文檔底部看到錯誤。
- **嘗試執行工件 時**。 這可能是因為網路或存儲問題。 有關詳細資訊，請參閱本文後面的相應部分。 由於腳本的創作方式，也可能發生這種情況。 例如：
    - PowerShell 腳本具有**強制參數**，但無法將值傳遞給它，或者因為允許使用者將其留空，或者因為專案檔案.json 定義檔中的屬性沒有預設值。 腳本將掛起，因為它正在等待使用者輸入。
    - PowerShell 腳本**需要使用者輸入**作為執行的一部分。 腳本必須編寫到靜默工作，而無需任何使用者干預。
- **VM 代理需要很長時間才能準備就緒**。 初次開機 VM 或首次安裝自訂腳本擴展以服務應用專案的請求時，VM 可能需要升級 VM 代理或等待 VM 代理初始化。 VM 代理所依賴的服務可能需要很長時間才能初始化。 在這種情況下，請參閱[Azure 虛擬機器代理概述](../virtual-machines/extensions/agent-windows.md)以進行進一步的故障排除。

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>驗證專案是否由於腳本而掛起

1. 登錄到有問題的虛擬機器。
2. 在虛擬機器中本地複製腳本，或在 下的`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`虛擬機器上找到它。 它是下載工件腳本的位置。
3. 使用提升的命令提示，在本地執行腳本，提供用於導致問題的相同參數值。
4. 確定腳本是否受到任何不需要的行為。 如果是這樣，請請求對專案進行更新（如果更新來自公共存儲庫）;或者，自己進行更正（如果是來自您的私人回購）。

> [!TIP]
> 您可以更正我們[公開存儲庫](https://github.com/Azure/azure-devtestlab)中託管的工件問題，並將更改提交以供我們審核和批准。 請參閱[README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)文檔中的 **"貢獻**"部分。
> 
> 有關編寫自己的工件的資訊，請參閱[AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md)文檔。

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>要驗證專案是否由於 VM 代理而掛起：
1. 登錄到有問題的虛擬機器。
2. 使用檔資源管理器導航到**C：\WindowsAzure_logs**。
3. 查找並打開檔**WaAppAgent.log**。
4. 查找顯示 VM 代理何時啟動以及何時完成初始化（即發送第一個活動訊號）的條目。 青睞較新的條目，或者特別選擇您遇到問題的時間段周圍的條目。

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    在此示例中，您可以看到 VM 代理啟動時間耗時 10 分鐘 20 秒，因為已發送活動訊號。 在這種情況下，原因就是 OOBE 服務需要很長時間才能啟動。

> [!TIP]
> 有關 Azure 擴展的一般資訊，請參閱[Azure 虛擬機器擴展和功能](../virtual-machines/extensions/overview.md)。

## <a name="storage-errors"></a>存儲錯誤
DevTest 實驗室需要訪問為緩存專案而創建的實驗室存儲帳戶。 當 DevTest Labs 應用專案時，它將從配置的存儲庫中讀取專案配置及其檔。 預設情況下，DevTest 實驗室配置對**公共專案存儲庫**的訪問。

根據 VM 的配置方式，它可能無法直接存取此回購。 因此，DevTest Labs 設計中緩存在首次初始化實驗室時創建的存儲帳戶中的專案。

如果以任何方式阻止對此存儲帳戶的訪問，例如當流量從 VM 阻止到 Azure 存儲服務時，您可能會看到類似于以下錯誤：

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

上述錯誤將顯示在"管理專案 **"下的"專案結果**"頁中的 **"部署消息****"部分中**。 它還將顯示在相關虛擬機器的資源組下**的活動日誌**中。

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>為確保未阻止與 Azure 存儲服務的通信，

- **檢查已添加的網路安全性群組 （NSG）。** 可能是添加了訂閱策略，其中 NSG 在所有虛擬網路中自動設定。 它還會影響實驗室的預設虛擬網路（如果使用）或實驗室中配置用於創建 VM 的其他虛擬網路。
- **檢查預設實驗室的存儲帳戶**（即創建實驗室時創建的第一個\<存儲帳戶，其名稱通常以字母"a"開頭，以多位數數位即實驗室名稱\>*結尾）。
    1. 導航到實驗室的資源組。
    2. 查找類型**存儲帳戶**的資源，其名稱與約定匹配。
    3. 導航到名為**防火牆和虛擬網路的**存儲帳戶頁面。
    4. 確保將其設置為 **"所有網路**"。 如果選擇了 **"選定網路**"選項，則確保將用於創建 VM 的實驗室虛擬網路添加到清單中。

有關更深入的故障排除，請參閱[配置 Azure 存儲防火牆和虛擬網路](../storage/common/storage-network-security.md)。

> [!TIP]
> **驗證網路安全性群組規則**。 使用[IP 流驗證](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify)確認網路安全性群組中的規則是否阻止進出虛擬機器的流量。 您還可以查看有效的安全性群組規則，以確保存在入站**允許**NSG 規則。 如需詳細資訊，請參閱[使用有效安全性規則對 VM 流量流程進行疑難排解](../virtual-network/diagnose-network-traffic-filter-problem.md)。

## <a name="other-sources-of-error"></a>其他錯誤來源
還有其他不太常見的錯誤源。 請確保評估每個情況，以查看其是否適用于您的案例。 下面是其中之一： 

- **已過期的個人訪問權杖為專用回購**。 過期後，專案將不會列出，並且引用具有過期私有訪問權杖的存儲庫中的工件的任何腳本都將相應失敗。

## <a name="next-steps"></a>後續步驟
如果這些錯誤均未發生，但仍無法應用專案，則可以提交 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。

