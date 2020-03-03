---
title: 我在 Azure 自動化中的第一個圖形化 Runbook
description: 教學課程將逐步引導您建立、測試和發佈簡單的圖形化 Runbook。
keywords: runbook, runbook 範本, runbook 自動化, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 351ed146511409fb4c6cc49705c7051b89ea0212
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226488"
---
# <a name="my-first-graphical-runbook"></a>我的第一個圖形化 runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

本教學課程將逐步引導您在 Azure 自動化中建立[圖形化 runbook](automation-runbook-types.md#graphical-runbooks) 。 從測試和發佈的簡單 runbook 開始，同時瞭解如何追蹤 runbook 作業的狀態。 然後修改 runbook 以實際管理 Azure 資源，在此案例中是啟動 Azure 虛擬機器。 完成本教學課程，藉由新增 runbook 參數和條件式連結，讓 runbook 更穩固。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 由於您會停止並啟動這部電腦，因此它不應該是生產 VM。

## <a name="step-1---create-runbook"></a>步驟 1 - 建立 Runbook

從建立會輸出 "Hello World" 文字的簡單 runbook 開始。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。 

    [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 這些資產大部分都是自動包含在新的自動化帳戶中的模組。 您也應該擁有與您的訂用帳戶相關聯的認證資產。
2. 選取 [程式**自動化**] 底下的 [ **runbook** ] 以開啟 runbook 清單。
3. 選取 [**建立 runbook**] 來建立新的 runbook。
4. 為 Runbook 提供名稱「MyFirstRunbook-Graphical」。
5. 在此情況下，您將建立[圖形化 runbook](automation-graphical-authoring-intro.md)。 選取 [**圖形**] 作為 [ **Runbook 類型**]。<br> ![新的 Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. 按一下 [建立] 來建立 Runbook 並開啟圖形化編輯器。

## <a name="step-2---add-activities"></a>步驟 2-新增活動

編輯器左邊的 [程式庫] 控制項可讓您選取要加入到 Runbook 的活動。 您將會新增 **Write-Output** Cmdlet，以從 Runbook 輸出文字。

1. 在 [程式庫] 控制項中，按一下 [搜尋] 欄位並輸入「**寫入-輸出**」。 下圖顯示搜尋結果。 <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 向下捲動到清單底部。 以滑鼠右鍵按一下 [**寫入-輸出**]，然後選取 [**新增至畫布**]。 或者，您可以按一下 Cmdlet 名稱旁的省略號（...），然後選取 [**新增至畫布**]。
1. 按一下畫布上的 **Write-Output** 活動。 此動作會開啟可讓您設定活動的 [設定] 控制項頁面。
1. [**標籤**] 欄位的預設值為 Cmdlet 的名稱，但您可以將它變更為更容易瞭解的專案。 將它變更為「Write Hello World to output」。
1. 按一下 [參數] 來提供 Cmdlet 的參數值。

   某些 Cmdlet 有多個參數集，您必須選取要使用哪一個。 在此情況下，**寫入輸出**只會設定一個參數。

1. 選取 *InputObject* 參數。 這是您用來指定要傳送至輸出資料流程之文字的參數。
1. [**資料來源**] 下拉式功能表會提供您可以用來填入參數值的來源。 在此功能表中，選取 [ **PowerShell 運算式**]。 

   您可以使用來自這類來源的輸出作為另一個活動、自動化資產或 PowerShell 運算式。 在此情況下，輸出就是 Hello World。 您可以使用 PowerShell 運算式，並指定字串。<br>

1. 在 [**運算式**] 欄位中，輸入**Hello World** ，然後按一下 **[確定]** 兩次以返回畫布。
1. 按一下 [儲存]來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook

在您發佈 runbook 以讓它可在生產環境中使用之前，您應該先進行測試，以確定它能正常運作。 測試 runbook 會執行其草稿版本，並可讓您以互動方式來查看其輸出。

1. 選取 [**測試窗格]** 以開啟 [測試] 窗格。
1. 按一下 [開始] 以開始測試。 這應該是唯一啟用的選項。
1. 請注意，會建立[runbook 工作](automation-runbook-execution.md)，並在窗格中顯示其狀態。

   作業狀態會以「已**排入佇列**」開始，表示作業正在等候雲端中的 runbook 背景工作可供使用。 當背景工作宣告作業時，狀態會變更為 [**正在啟動**]。 最後，當 runbook 實際開始執行時，狀態會變成 [**正在**執行]。

1. 當 runbook 作業完成時，[測試] 頁面會顯示其輸出。 在此情況下，您會看到 Hello World。<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您建立的 runbook 仍處於草稿模式。 您需要將它發佈，才能在生產環境中執行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 選取 [發佈] 來發佈 Runbook，然後在系統提示時選取 [是]。
1. 向左滾動以在 [Runbook] 頁面上查看 runbook，並注意 [**撰寫狀態**] 值設定為 [**已發佈**]。
1. 捲回右側，以檢視 **MyFirstRunbook-Graphical** 的頁面。

   在頂端的選項可讓您立即啟動 runbook、排程未來的開始時間，或建立[webhook](automation-webhooks.md) ，以便透過 HTTP 呼叫來啟動 runbook。

1. 選取 [開始]，然後在系統提示時選取 [是]，以啟動 Runbook。
1. 已建立的 runbook 作業會開啟 [作業] 窗格。 確認 [**作業狀態**] 欄位顯示 [**已完成**]。
1. 按一下 [**輸出**] 以開啟 [輸出] 頁面，您可以在其中查看顯示**Hello World** 。
1. 關閉 [輸出] 頁面。
1. 按一下 [所有記錄] 以開啟 Runbook 作業的 [資料流] 窗格。 您應該只會在輸出資料流程中看到**Hello World** 。 

    請注意，[資料流程] 窗格可以顯示 runbook 作業的其他資料流程，例如 [詳細資訊] 和 [錯誤資料流程] （如果 runbook 寫入它們）。
1. 關閉 [資料流程] 窗格和 [作業] 窗格，以返回**MyFirstRunbook-圖形化**頁面。
1. 若要查看 runbook 的所有作業，請選取 [**資源**] 下的 [**作業**]。 [作業] 頁面會列出您的 runbook 所建立的所有工作。 您應該只會看到一個列出的作業，因為您只會執行一次作業。
1. 按一下作業名稱，開啟您在啟動 runbook 時所看到的相同作業窗格。 使用此窗格來查看針對 runbook 所建立之任何作業的詳細資料。

## <a name="step-5---create-variable-assets"></a>步驟 5 - 建立變數資產

您已測試併發布您的 runbook，但到目前為止，它不會執行任何有助於管理 Azure 資源的專案。 將 runbook 設定為進行驗證之前，您必須先建立一個變數來保存訂用帳戶識別碼、設定要驗證的活動，然後參考該變數。 包含訂用帳戶內容的參考，可讓您輕鬆地使用多個訂閱。

1. 從流覽窗格的 [**訂閱**] 選項複製您的訂用帳戶識別碼。
1. 在 [自動化帳戶] 頁面中，選取 [**共用資源**] 底下的 [**變數**]。
1. 選取 [新增變數]。
1. 在 [新增變數] 頁面上，于提供的欄位中進行下列設定。

    * **名稱**-輸入**AzureSubscriptionId**。
    * **值**-輸入您的訂用帳戶識別碼。 
    * **類型**--保留選取的字串。
    * **加密**--使用預設值。
1. 按一下 [建立] 來建立變數。

## <a name="step-6---add-authentication"></a>步驟 6-新增驗證

現在您已有可保存訂用帳戶識別碼的變數，您可以將 runbook 設定為使用訂用帳戶的「執行身分」認證進行驗證。 若要這麼做，請將 Azure 執行身分連線新增為資產。 您也必須將[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) Cmdlet 和[set-azcoNtext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) Cmdlet 新增至畫布。

>[!NOTE]
>針對 PowerShell runbook， **disconnect-azaccount**和**add-AzureRMAccount**是**disconnect-azaccount**的別名。 請注意，您的圖形化 runbook 無法使用這些別名。 圖形化 runbook 只能使用**disconnect-azaccount**本身。

1. 流覽至您的 runbook，並在**MyFirstRunbook-圖形化**頁面上選取 [**編輯**]。
1. 您不需要**撰寫 Hello World 的輸出**專案。 只要按一下省略號，然後選取 [**刪除**]。
1. 在 [**連結**庫] 控制項中，依序展開 [**資產**]、[連線]。 選取 [**新增至畫布**]，將**AzureRunAsConnection**新增至畫布。
1. 在 [**程式庫]** 控制項的 [搜尋] 欄位中，輸入 disconnect-azaccount。
1. 將 **[Disconnect-azaccount]** 新增至畫布。
1. 將滑鼠停留在 [取得執行身分連線]，直到圖形的底端出現圓形。 按一下圓形，並將箭號拖曳至 **[Disconnect-azaccount]** 以形成連結。 Runbook 的開頭為 [**取得執行**身分連線]，然後執行 **[disconnect-azaccount]** 。<br> ![建立活動之間的連結](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 在畫布上，選取 [ **Disconnect-azaccount]** 。 在 [設定控制] 窗格中，于 [**標籤**] 欄位中輸入 [**登入 Azure** ]。
1. 按一下 [**參數**]，[活動參數設定] 頁面隨即出現。
1. **Disconnect-azaccount 指令程式**有多個參數集，您必須先選取其中一個，才能提供參數值。 按一下 [參數集]，然後選取 [ServicePrincipalCertificate] 參數集。
1. 此參數集的參數會顯示在 [活動參數設定] 頁面上。 按一下 [APPLICATIONID]。<br> ![新增 Azure 帳戶參數](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 在 [參數值] 頁面上，進行下列設定，然後按一下 **[確定]** 。

   * **資料來源**--選取**活動輸出**。
   * 資料來源清單-選取 [**取得執行**身分連線]。
   * **欄位路徑**--類型**ApplicationId**。 您要指定欄位路徑的屬性名稱，因為活動會輸出具有多個屬性的物件。
1. 按一下 [ **CERTIFICATETHUMBPRINT**]，在 [參數值] 頁面上進行下列設定，然後按一下 **[確定]** 。

    * **資料來源**--選取**活動輸出**。
    * 資料來源清單-選取 [**取得執行**身分連線]。
    * **欄位路徑**--類型**CertificateThumbprint**。
1. 按一下 [ **SERVICEPRINCIPAL**]，然後在 [參數值] 頁面上，針對 [**資料來源**] 欄位選取 [ **[constantvalue]** ]。按一下 [ **True**] 選項;然後按一下 **[確定]** 。
1. 按一下 [ **TENANTID**]，然後在 [參數值] 頁面上進行下列設定。 完成後，請按兩次 **[確定]** 。

    * **資料來源**--選取**活動輸出**。 
    * 資料來源清單-選取 [**取得執行**身分連線]。
    * **欄位路徑**--類型**TenantId**。 
1. 在 [程式庫] 控制項的 [搜尋] 欄位中輸入**Set-set-azcoNtext** 。
1. 將**set-azcoNtext**新增至畫布。
1. 選取畫布上的 [ **set-azcoNtext** ]。 在 [設定控制] 窗格的 [**標籤**] 欄位中，輸入 [**指定訂**用帳戶識別碼]。
1. 按一下 [參數]，隨即會顯示 [Activity Parameter Configuration] \(活動參數設定) 頁面。
1. **Set-azcoNtext 指令程式**有多個參數集，您必須在提供參數值之前選取一個。 按一下 [參數集]，然後選取 [SubscriptionId] 參數集。
1. 此參數集的參數會顯示在 [活動參數設定] 頁面上。 按一下 [ **SubscriptionID**]。
1. 在 [參數值] 頁面上，選取 [**資料來源**] 欄位的 [**變數資產**]，然後從 [來源] 清單中選取 [ **AzureSubscriptionId** ]。 完成後，請按兩次 **[確定]** 。
1. 將滑鼠停留在 [登入 Azure] ，直到圖形的底端出現圓形。 按一下圓形，並將箭頭拖曳到 [指定訂用帳戶識別碼]。 

此時您的 Runbook 看起來應該像下面這樣︰ <br>![Runbook 驗證組態](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>步驟 7 - 加入活動以啟動虛擬機器

現在您必須新增**開始 update-azvm**活動，以啟動虛擬機器。 您可以在 Azure 訂用帳戶中挑選任何 VM，而現在您會將它的名稱硬式編碼到[update-azvm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)指令程式中。

1. 在 [程式庫] 控制項中，于搜尋欄位中輸入**Start-Az** 。
2. 將 [**開始-update-azvm** ] 新增至畫布，然後按一下並將它拖曳到 [**指定訂**用帳戶識別碼] 底下。
1. 將滑鼠停留在 [指定訂用帳戶識別碼] ，直到圖形的底端出現圓形。 按一下圓形，並將箭號拖曳至 [**開始-update-azvm**]。
1. 選取 [**開始-update-azvm**]。 按一下 [**參數**]，然後按 [**參數集**] 以查看活動的集合。
1. 選取 [ResourceGroupNameParameterSetName] 參數集。 欄位**ResourceGroupName**和**名稱**旁邊都有驚嘆號，表示它們是必要參數。 請注意，這兩個欄位都預期為字串值。
1. 選取 [Start-AzureRmVM]。 選擇 [**資料來源**] 欄位的 [ **PowerShell 運算式**]。 針對您用來啟動此 runbook 的 VM，請輸入以雙引號括住的電腦名稱稱。 按一下 [確定]。
1. 選取 [ResourceGroupName]。 針對 [**資料來源**] 欄位使用 [ **PowerShell 運算式**] 值，然後輸入以雙引號括住的資源組名。 按一下 [確定]。
1. 按一下 [測試] 窗格，您便可測試 Runbook。
1. 按一下 [**啟動**] 開始測試。 完成後，請確定 VM 已啟動。 

此時您的 Runbook 看起來應該像下面這樣︰ <br>![Runbook 驗證組態](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>步驟 8-新增其他輸入參數

您的 runbook 目前會在您為**update-azvm** Cmdlet 指定的資源群組中啟動 VM。 如果您在 runbook 啟動時同時指定名稱和資源群組，runbook 將會更有用。 讓我們將輸入參數新增至 runbook，以提供該功能。

1. 按一下 [MyFirstRunbook-Graphical] 窗格上的 [編輯] 來開啟圖形化編輯器。
1. 選取 [輸入和輸出]，然後選取 [新增輸入]，來開啟 [Runbook Input Parameter] \(Runbook 輸入參數) 窗格。
1. 在提供的欄位中進行下列設定，然後按一下 **[確定]** 。
   * **名稱**-指定**VMName**。
   * **輸入**--保留字元串設定。
   * **強制**--將值變更為 **[是]** 。
1. 建立名為*ResourceGroupName*的第二個必要輸入參數，然後按一下 **[確定]** 以關閉 [輸入和輸出] 窗格。<br> ![Runbook 輸入參數](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. 選取 [**開始-update-azvm** ] 活動，然後按一下 [**參數**]。
1. 將 [**名稱**] 的 [**資料來源**] 欄位變更為 [ **Runbook 輸入**]。 然後選取 [ **VMName**]。
1. 將 [ **ResourceGroupName** ] 的 [**資料來源**] 欄位變更為 [ **Runbook 輸入**]，然後選取 [ **ResourceGroupName**]。<br> ![開始 Update-azvm 參數](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中使用的兩個輸入變數的值。
1. 關閉 [測試] 窗格。
1. 按一下 [發佈] 來發行新版本的 Runbook。
1. 停止您先前啟動的 VM。
1. 按一下 [開始] 以啟動 Runbook。 針對您即將啟動的 VM，輸入**VMName**和**ResourceGroupName**的值。
1. 當 runbook 完成時，請確定 VM 已啟動。

## <a name="step-9---create-a-conditional-link"></a>步驟 9 - 建立條件式連結

您現在可以修改 runbook，讓它只會嘗試啟動尚未啟動的 VM。 若要這麼做，請新增可抓取 VM 實例層級狀態的[update-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) Cmdlet。 接著，您可以使用 PowerShell 程式碼的程式碼片段來新增名為「**取得狀態**」的 PowerShell 工作流程程式碼模組，以判斷 VM 狀態為執行中或已停止。 只有當目前的執行狀態為 [已停止] 時，來自 [**取得狀態**] 模組的條件式連結才會執行**update-azvm** 。 在此程式結束時，您的 runbook 會使用**寫入輸出**Cmdlet 來輸出訊息，以通知您 VM 是否已成功啟動。

1. 在圖形化編輯器中開啟 **MyFirstRunbook-Graphical**。
1. 按一下 [**指定訂**用帳戶識別碼] 和 [ **update-azvm** ]，然後按下 [**刪除**]，以移除其連結。
1. 在 [程式庫] 控制項中，于 [搜尋] 欄位中輸入**Get-Az** 。
1. 將**update-azvm**新增至畫布。
1. 選取 [ **update-azvm** ]，然後選取 [**參數集**] 以查看 Cmdlet 的集合。 
1. 選取 [GetVirtualMachineInResourceGroupNameParamSet] 參數集。 [ **ResourceGroupName** ] 和 [**名稱**] 欄位旁邊有驚嘆號，表示它們指定必要的參數。 請注意，這兩個欄位都預期為字串值。
1. 在 [**名稱**] 的 [**資料來源**] 底下，選取 [ **Runbook 輸入**]，然後**VMName**。 按一下 [確定]。
1. 在**ResourceGroupName**的 [**資料來源**] 底下，依序選取 [ **Runbook 輸入**] 和 [ **ResourceGroupName**]。 按一下 [確定]。
1. 在 [**狀態**] 的 [**資料來源**] 底下，選取 [**常數值**] 和 [ **True**]。 按一下 [確定]。
1. 建立從**指定訂**用帳戶識別碼到**update-azvm**的連結。
1. 在 [程式庫] 控制項中，展開 [ **Runbook 控制**]，並將程式**代碼**新增至畫布。  
1. 建立從 Update-azvm 到**程式** **代碼**的連結。  
1. 按一下 [程式**代碼**]，然後在 [設定] 窗格中，將標籤變更為 [**取得狀態**]。
1. 選取 [程式**代碼**]，[程式碼編輯器] 頁面隨即出現。  
1. 將下列程式碼片段貼到編輯器頁面中。

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

1. 建立從 [**取得狀態**] 到 [**開始-update-azvm**] 的連結。<br> ![程式碼模組的 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 選取連結，然後在 [設定] 窗格中，將 [套用**條件**] 變更為 **[是]** 。 請注意，此連結會變成虛線，表示目標活動只有在條件解析為 true 時才會執行。  
1. 在 [**條件運算式**] 中，輸入 `$ActivityOutput['Get Status'] -eq "Stopped"`。 **開始-update-azvm**現在只會在 VM 停止時執行。
1. 在 [程式庫] 控制項中，展開 **Cmdlet**，然後展開 **Microsoft.PowerShell.Utility**。
1. 將 [Write-Output] 新增至畫布兩次。
1. 針對第一個**寫入輸出**控制項，按一下 [**參數**]，然後將 [**標籤**] 值變更為 [**通知 VM 已啟動**]。
1. 若是**InputObject**，請將**資料來源**變更為**PowerShell 運算式**，然後在運算式中輸入 **$VMName 成功啟動。**
1. 在第二個**寫入輸出**控制項上，按一下 [**參數**]，然後將 [**標籤**] 值變更為 [**通知 VM 啟動失敗**]。
1. 若是**InputObject**，請將**資料來源**變更為**PowerShell 運算式**，然後在運算式中輸入 **$VMName 無法啟動。**
1. 建立**update-azvm**的連結以**通知 vm 已啟動**，並**通知 vm 啟動失敗**。
1. 選取 [**通知 VM 已啟動**] 連結，並將 [套用**條件**] 變更為 [true]。
1. 在 [**條件運算式**] 中，輸入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`。 此**寫入輸出**控制項現在只有在 VM 成功啟動時才會執行。
1. 選取 [**通知 VM 啟動失敗**] 連結，並將 [套用**條件**] 變更為 [true]。
1. 在 [**條件運算式**] 欄位中，輸入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 此**寫入輸出**控制項現在只會在 VM 未成功啟動時執行。 您的 Runbook 應該與下圖類似︰ <br> ![Write-Output 的 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. 儲存 Runbook 並開啟 [測試] 窗格。
1. 啟動已停止 VM 的 runbook，電腦應該會啟動。

## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化編寫，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
* 若要開始使用 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要開始使用 PowerShell 工作流程 runbook，請參閱[我的第一個 powershell 工作流程 runbook](automation-first-runbook-textual.md)。