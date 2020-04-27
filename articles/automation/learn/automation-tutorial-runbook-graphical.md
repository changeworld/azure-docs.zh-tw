---
title: 在 Azure 自動化中建立圖形化 Runbook
description: 示範如何在 Azure 自動化中建立、測試及發佈簡單圖形化 Runbook 的教學課程。
keywords: runbook, runbook 範本, runbook 自動化, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: f87f389667043e26f066886eddcdb8061df0319f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81725122"
---
# <a name="tutorial-create-a-graphical-runbook"></a>教學課程：建立圖形化 Runbook

本教學課程將逐步引導您在 Azure 自動化中建立 [圖形化 Runbook](../automation-runbook-types.md#graphical-runbooks) 。 您可以使用 Azure 入口網站中的圖形化編輯器，建立和編輯圖形化和圖形化 PowerShell 工作流程 Runbook。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立簡單圖形化 Runbook
> * 測試和發佈 Runbook
> * 執行和追蹤 Runbook 作業的狀態
> * 更新 Runbook 以使用 Runbook 參數和條件式連結啟動 Azure 虛擬機器

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](../automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 您會停止並啟動這部電腦，因此其不該是生產 VM。

## <a name="step-1---create-runbook"></a>步驟 1 - 建立 Runbook

由建立一個可輸出文字 `Hello World` 的簡單 Runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。 

    [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 其中大部分資產是自動包含在新自動化帳戶的模組。 您也應該擁有與您的訂用帳戶相關聯的認證資產。

2. 選取 [程序自動化]  底下的 [Runbook]  ，以開啟 Runbook 清單。

3. 藉由選取 [建立 Runbook]  來建立新的 Runbook。

4. 為 Runbook 提供名稱「MyFirstRunbook-Graphical」  。

5. 在此情況下，您即將建立[圖形化 Runbook](../automation-graphical-authoring-intro.md)。 針對 [Runbook 類型]  選取 [圖形]  。<br> ![新的 Runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)<br>

6. 按一下 [建立]  來建立 Runbook 並開啟圖形化編輯器。

## <a name="step-2---add-activities"></a>步驟 2 - 新增活動

編輯器左邊的 [程式庫] 控制項可讓您選取要加入到 Runbook 的活動。 您將會新增 `Write-Output` Cmdlet，以從 Runbook 輸出文字。

1. 在 [程式庫] 控制項中，按一下搜尋欄位並輸入 `write-output`。 搜尋結果會顯示於下面的影像中。 <br> ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. 向下捲動到清單底部。 以滑鼠右鍵按一下 **Write-Output** 並選取 [新增至畫布]  。 或者，您可以按一下 Cmdlet 名稱旁的省略符號 (...)，然後選取 [新增至畫布]  。

3. 按一下畫布上的 **Write-Output** 活動。 此動作會開啟可讓您設定活動的 [設定] 控制項頁面。

4. [標籤]  欄位會預設為 Cmdlet 的名稱，但您可將其變更為比較好記的名稱。 將其變更為 `Write Hello World to output`。

5. 按一下 [參數]  來提供 Cmdlet 的參數值。

   某些 Cmdlet 有多個參數集，並且您必須選取要使用的參數。 在此情況下，`Write-Output` 只有一個參數集。

6. 選取 `InputObject` 參數。 您會使用這個參數來指定要傳送至輸出資料流的文字。

7. [資料來源]  下拉式功能表會提供您用來填入參數值的來源。 在此功能表中，選取 [PowerShell 運算式]  。 

   您可以使用來自這類來源的輸出，例如另一個活動、自動化資產或 PowerShell 運算式。 在此情況下，輸出就是 `Hello World`。 您可以使用 PowerShell 運算式，並指定字串。<br>

8. 在 [運算式]  欄位中，輸入 `Hello World`，然後按一下 [確定]  兩次以返回畫布。

9. 按一下 [儲存]  來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook

在您發佈 Runbook 之前，為了使其可用於生產環境，您應該進行測試以確定其可正常運作。 測試 Runbook 時，系統會執行其「草稿」版本並可讓您以互動方式檢視其輸出。

1. 選取 [測試窗格]  來開啟 [測試] 窗格。

2. 按一下 [開始]  以開始測試。 這應該是唯一啟用的選項。

3. 請注意，系統會建立 [Runbook 作業](../automation-runbook-execution.md)並在窗格中顯示其狀態。

   作業狀態一開始為 `Queued`，表示該作業正在等候雲端中的 Runbook 背景工作可供使用。 當背景工作腳色宣告該作業時，狀態會變成 `Starting`。 最後，當 Runbook 實際開始執行時，狀態會變成 `Running`。

4. 當 Runbook 作業完成時，測試窗格會顯示其輸出。 在此情況下，您可看見 `Hello World`。

    ![Hello World](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您建立的 Runbook 仍處於草稿模式。 您需要將它發佈，才能在生產環境中執行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 選取 [發佈]  來發佈 Runbook，然後在系統提示時選取 [是]  。

2. 向左捲動以在 [Runbook] 頁面上流檢視 Runbook，並請注意 [撰寫狀態]  值會設為 [已發佈]  。

3. 捲回右側，以檢視 **MyFirstRunbook-Graphical** 的頁面。

   頂端的選項可讓您立即啟動 Runbook、排程在未來啟動時間，或建立 [Webhook](../automation-webhooks.md)，以便透過 HTTP 呼叫啟動該 Runbook。

4. 選取 [開始]  ，然後在系統提示時選取 [是]  ，以啟動 Runbook。

5. [作業] 窗格會針對已建立的 Runbook 作業開啟。 確認 [作業狀態]  欄位顯示 [已完成]  。

6. 按一下 [輸出]  以開啟 [輸出] 頁面，您可在其中看到 `Hello World` 顯示出來。

7. 關閉 [輸出] 頁面。

8. 按一下 [所有記錄]  以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流中看到 `Hello World`。 

    請注意，[資料流] 窗格可顯示 Runbook 作業的其他資料流，例如 Runbook 寫入時出現的詳細資料和錯誤資料流。

9. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook-Graphical 頁面。

10. 若要檢視 Runbook 的所有作業，請選取 [資源]  下方的 [作業]  。 [作業] 頁面會列出 Runbook 建立的所有作業。 由於您只執行一次作業，所以應該只會看到一項作業列出。

11. 按一下作業名稱，以開啟您啟動 Runbook 時所檢視的相同 [作業] 窗格。 使用此窗格來檢視針對 Runbook 建立的任何作業詳細資料。

## <a name="step-5---create-variable-assets"></a>步驟 5 - 建立變數資產

您已測試並發佈您的 Runbook，但是到目前為止，其似乎不適合用於管理 Azure 資源。 在設定要驗證的 Runbook 之前，您必須建立變數來保存訂用帳戶識別碼，設定要驗證的活動，然後參考此變數。 包括訂用帳戶內容的參考，可讓您輕鬆地處理多個訂用帳戶。

1. 請從 [導覽] 窗格上的 [訂用帳戶]  選項複製您的訂用帳戶識別碼。

2. 在 [自動化帳戶] 頁面中，選取 [共用資源]  下方的 [變數]  。

3. 選取 [新增變數]  。

4. 在 [新增變數] 頁面上，在所提供的欄位中進行下列設定。

    * **名稱**：輸入 `AzureSubscriptionId`。
    * **值**：輸入您的訂用帳戶 ID。 
    * **類型**：保留選取的字串。
    * **加密**：使用預設值。

5. 按一下 [建立]  來建立變數。

## <a name="step-6---add-authentication"></a>步驟 6 - 新增驗證

既然您已擁有用來保存訂用帳戶識別碼的變數，您可設定 Runbook，以使用執行身分認證來驗證您的訂用帳戶。 若要這麼做，請將 Azure 執行身分連線新增為資產。 您也必須將 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) Cmdlet 和 [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) Cmdlet 新增至畫布。

>[!NOTE]
>針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 請注意，您的圖形化 Runbook 無法使用這些別名。 圖形化 Runbook 只能使用 `Connect-AzAccount` 本身。

1. 巡覽至您的 Runbook，並在 MyFirstRunbook-Graphical 頁面上選取 [編輯]  。

2. 您不再需要 `Write Hello World to output` 項目。 只要按一下省略符號並選取 [刪除]  。

3. 在 [程式庫] 控制項中，展開 [資產]  ，然後展開 [連線]  。 選取 [新增至畫布]  ，以將 `AzureRunAsConnection` 新增至畫布。

4. 將 `AzureRunAsConnection` 重新命名為 `Get Run As Connection`。

5. 在 [程式庫] 控制項中，於搜尋欄位中輸入 `Connect-AzAccount`。

6. 將 `Connect-AzAccount` 新增至畫布。

7. 將滑鼠停留在 `Get Run As Connection` 上，直到圖形底端出現圓形為止。 按一下圓形，然後將箭頭拖曳到 `Connect-AzAccount` 以形成連結。 Runbook 會從 `Get Run As Connection` 開始，然後執行 `Connect-AzAccount`。<br> ![建立活動之間的連結](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. 在畫布上選取 `Connect-AzAccount`。 在 [設定控制項] 窗格的 [標籤]  欄位中，輸入 [登入 Azure]  。

9. 按一下 [參數]  ，隨即會顯示 [活動參數設定] 頁面。

10. `Connect-AzAccount` Cmdlet 有多個參數集，因此您必須先選取一個參數集，才能提供參數值。 按一下 [參數集]  ，然後選取 [ServicePrincipalCertificateWithSubscriptionId]  。

11. 此參數集的參數會顯示在 [活動參數設定] 頁面上。 按一下 [APPLICATIONID]  。<br> ![新增 Azure 帳戶參數](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. 在 [參數值] 頁面上，進行下列設定，然後按一下 [確定]  。

   * **資料來源**：選取 [活動輸出]  。
   * 資料來源清單：選取 [取得自動化連線]  。
   * **欄位路徑**：輸入 `ApplicationId`。 您會指定欄位路徑之屬性的名稱，因為活動會輸出具有多個屬性的物件。

13. 按一下 [CERTIFICATETHUMBPRINT]  ，接著在 [參數值] 頁面上進行下列設定，然後按一下 [確定]  。

    * **資料來源**：選取 [活動輸出]  。
    * 資料來源清單：選取 [取得自動化連線]  。
    * **欄位路徑**：輸入 `CertificateThumbprint`。

14. 按一下 [SERVICEPRINCIPAL]  ，並在 [參數值] 頁面中，針對 [資料來源]  欄位選取 [ConstantValue]  ，再按一下選項 [True]  ，然後按一下 [確定]  。

15. 按一下 [TENANTID]  ，然後在 [參數值] 頁面上進行下列設定。 完成後，請按兩次 [確定]  。

    * **資料來源**：選取 [活動輸出]  。 
    * 資料來源清單：選取 [取得自動化連線]  。
    * **欄位路徑**：輸入 `TenantId`。 

16. 在 [程式庫] 控制項中，於搜尋欄位中輸入 `Set-AzContext`。

17. 將 `Set-AzContext` 新增至畫布。

18. 在畫布上選取 `Set-AzContext`。 在 [設定控制項] 窗格的 [標籤]  欄位中，輸入 `Specify Subscription Id`。

19. 按一下 [參數]  ，隨即會顯示 [Activity Parameter Configuration] \(活動參數設定) 頁面。

20. `Set-AzContext` Cmdlet 有多個參數集，因此您必須先選取一個參數集，才能提供參數值。 按一下 [參數集]  ，然後選取 [SubscriptionId]  。

21. 此參數集的參數會顯示在 [活動參數設定] 頁面上。 按一下 [SubscriptionID]  。

22. 在 [參數值] 頁面上，針對 [資料來源]  欄位選取 [變數資產]  ，然侯從來源清單中選取 [AzureSubscriptionId]  。 完成後，請按兩次 [確定]  。

23. 將滑鼠停留在 `Login to Azure` 上，直到圖形底端出現圓形為止。 按一下圓形，然後將箭頭拖曳到 `Specify Subscription Id`。 此時您的 Runbook 看起來應該像下面這樣。

    ![Runbook 驗證組態](../media/automation-tutorial-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>步驟 7 - 加入活動以啟動虛擬機器

您現在必須新增 `Start-AzVM` 活動以啟動虛擬機器。 您可以在您的 Azure 訂用帳戶中挑選任何 VM，而現在您會將其名稱硬式編碼到 [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) Cmdlet 中。

1. 在 [程式庫] 控制項中，於搜尋欄位中輸入 `Start-Az`。

2. 將 `Start-AzVM` 新增至畫布，然後按一下並將其拖曳至 `Specify Subscription Id` 下方。

3. 將滑鼠停留在 `Specify Subscription Id` 上，直到圖形底端出現圓形為止。 按一下圓形，然後將箭頭拖曳到 `Start-AzVM`。

4. 選取 `Start-AzVM`。 按一下 [參數]  ，然後按一下 [參數集]  以檢視活動的參數集。

5. 選取參數集的 [ResourceGroupNameParameterSetName]  。 [ResourceGroupName]  和 [名稱]  欄位旁都有驚嘆號，表示其為必要參數。 請注意，這兩個欄位都需要有字串值。

6. 選取 [Start-AzureRmVM]  。 針對 [資料來源]  欄位選擇 [PowerShell 運算式]  。 針對您用來啟動此 Runbook 的 VM，請輸入以雙引號括住的電腦名稱。 按一下 [確定]  。

7. 選取 [ResourceGroupName]  。 針對 [資料來源]  欄位使用 [PowerShell 運算式]  值，然後輸入用雙引號括住的資源群組名稱。 按一下 [確定]  。

8. 按一下 [測試]  窗格，您便可測試 Runbook。

9. 按一下 [開始]  以開始測試。 當測試完成後，請確定 VM 已啟動。 此時您的 Runbook 看起來應該像下面這樣。

    ![Runbook 驗證組態](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>步驟 8 -新增其他輸入參數

您的 Runbook 目前會啟動在 `Start-AzVM` Cmdlet 中所指定資源群組內的 VM。 如果您在 Runbook 啟動時同時指定名稱和資源群組，Runbook 將會更加實用。 讓我們將輸入參數新增至 Runbook，以提供該功能。

1. 按一下 [MyFirstRunbook-Graphical] 頁面上的 [編輯]  來開啟圖形化編輯器。

2. 選取 [輸入和輸出]  ，然後選取 [新增輸入]  ，來開啟 [Runbook Input Parameter] \(Runbook 輸入參數) 窗格。

3. 在所提供的欄位中進行下列設定，然後按一下 [確定]  。
   * **名稱**：指定 `VMName`。
   * **類型**：保留字串設定。
   * **必要**：將值變更為 [是]  。

4. 建立稱為 `ResourceGroupName` 的第二個必要輸入參數，然後按一下 [確定]  來關閉 [輸入和輸出] 窗格。<br> ![Runbook 輸入參數](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. 選取 `Start-AzVM` 活動，然後按一下 [參數]  。

6. 將 [名稱]  的 [資料來源]  欄位變更為 [Runbook 輸入]  。 然後選取 [VMName]  。

7. 將 [ResourceGroupName]  的 [資料來源]  欄位變更為 [Runbook 輸入]  ，然後選取 [ResourceGroupName]  。<br> ![Start-AzVM 參數](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中使用的兩個輸入變數的值。

9. 關閉 [測試] 窗格。

10. 按一下 [發佈]  來發行新版本的 Runbook。

11. 停止您先前啟動的 VM。

12. 按一下 [開始]  以啟動 Runbook。 針對您即將啟動的 VM，輸入 `VMName` 和 `ResourceGroupName` 的值。

13. 當 Runbook 完成時，請確定 VM 已啟動。

## <a name="step-9---create-a-conditional-link"></a>步驟 9 - 建立條件式連結

您現在可以修改 Runbook，使其只會在 VM 尚未啟動時嘗試加以啟動。 若要這麼做，請新增 [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) Cmdlet 以擷取 VM 的執行個體層級狀態。 然後您可新增名為 `Get Status` 的 PowerShell 工作流程程式碼模組與 PowerShell 程式碼片段，以判斷 VM 狀態為執行中還是已停止。 只有當目前執行中狀態為已停止時，`Get Status`模組中的條件式連結才會執行 `Start-AzVM`。 在此程序結束時，您的 Runbook 會使用 `Write-Output` Cmdlet 來輸出訊息，以通知您 VM 是否已成功啟動。

1. 在圖形化編輯器中開啟 **MyFirstRunbook-Graphical**。

2. 按一下 `Specify Subscription Id` 與 `Start-AzVM` 之間的連結，然後按 [刪除]  ，以移除該連結。

3. 在 [程式庫] 控制項中，於搜尋欄位中輸入 `Get-Az`。

4. 將 `Get-AzVM` 新增至畫布。

5. 選取 `Get-AzVM`，然後按一下 [參數集]  以檢視 Cmdlet 的集合。 

6. 選取 [GetVirtualMachineInResourceGroupNameParamSet]  參數集。 [ResourceGroupName]  和 [名稱]  欄位旁都有驚嘆號，表示其指定必要的參數。 請注意，這兩個欄位都需要有字串值。

7. 在 [名稱]  的 [資料來源]  底下，選取 [Runbook 輸入]  ，然後選取 [VMName]  。 按一下 [確定]  。

8. 在 [ResourceGroupName]  的 [資料來源]  底下，選取 [Runbook 輸入]  ，然後選取 [ResourceGroupName]  。 按一下 [確定]  。

9. 在 [狀態]  的 [資料來源]  底下，選取 [常數值]  ，然後選取 [True]  。 按一下 [確定]  。

10. 建立從 `Specify Subscription Id` 到 `Get-AzVM` 的連結。

11. 在 [程式庫] 控制項中展開 [Runbook 控制項]  ，然後將 [程式碼]  新增至畫布。  

12. 建立從 `Get-AzVM` 到 `Code` 的連結。  

13. 按一下 `Code`，然後在 [設定] 窗格中將標籤變更為 [取得狀態]  。

14. 選取 `Code`，[程式碼編輯器] 頁面隨即出現。  

15. 將以下程式碼片段貼到編輯器頁面中。

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

16. 建立從 `Get Status` 到 `Start-AzVM` 的連結。

    ![具有程式碼模組的 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. 選取該連結，然後在 [設定] 窗格中將 [套用條件]  變更為 [是]  。 請注意，連結會變成虛線，表示目標活動只有在條件解析為 true 時才會執行。  

18. 針對 [條件運算式]  ，輸入 `$ActivityOutput['Get Status'] -eq "Stopped"`。 如果 VM 已停止，則輸入 `Start-AzVM`。

19. 在 [程式庫] 控制項中，展開 **Cmdlet**，然後展開 **Microsoft.PowerShell.Utility**。

20. 將 `Write-Output` 新增至畫布兩次。

21. 針對第一個 `Write-Output` 控制項，按一下 [參數]  ，然後將 [標籤]  值變更為 [通知 VM 已啟動]  。

22. 在 [InputObject]  中將 [資料來源]  變更為 [PowerShell 運算式]  ，然後輸入運算式 `$VMName successfully started.`。

23. 在第二個 `Write-Output` 控制項上，按一下 [參數]  ，然後將 [標籤]  值變更為 [通知 VM 啟動失敗]  。

24. 在 [InputObject]  中將 [資料來源]  變更為 [PowerShell 運算式]  ，然後輸入運算式 `$VMName could not start`。

25. 建立從 `Start-AzVM` 到 `Notify VM Started` 和 `Notify VM Start Failed` 的連結。

26. 選取連往 `Notify VM Started` 的連結，然後將 [套用條件]  變更為 True。

27. 針對 [條件運算式]  ，輸入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`。 此 `Write-Output` 控制項現在只會在 VM 啟動成功時執行。

28. 選取連往 `Notify VM Start Failed` 的連結，然後將 [套用條件]  變更為 True。

29. 針對 [條件運算式]  欄位，輸入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 此 `Write-Output` 控制項現在只會在 VM 未啟動成功時執行。 您的 Runbook 應該與下圖類似。

    ![具有 Write-Output 的 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. 儲存 Runbook 並開啟 [測試] 窗格。

31. 在 VM 已停止的狀態下啟動 Runbook，該電腦應會啟動。

## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化編寫，請參閱 [Azure 自動化中的圖形化編寫](../automation-graphical-authoring-intro.md)。
* 若要開始使用 PowerShell Runbook，請參閱[建立 PowerShell Runbook](automation-tutorial-runbook-textual-powershell.md)。
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱[建立 PowerShell 工作流程 Runbook](automation-tutorial-runbook-textual.md)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。