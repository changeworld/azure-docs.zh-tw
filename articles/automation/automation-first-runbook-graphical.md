---
title: 我在 Azure 自動化中的第一個圖形化 Runbook
description: 教學課程將逐步引導您建立、測試和發佈簡單的圖形化 Runbook。
keywords: runbook, runbook 範本, runbook 自動化, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: bcef0574e16e0b4d28755716c32670b00c65af14
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406097"
---
# <a name="my-first-graphical-runbook"></a>我的第一個圖形化 Runbook

> [!div class="op_single_selector"]
> * [圖形化](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 工作流程](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

本教學課程將逐步引導您在 Azure 自動化中建立 [圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 。 從可以測試和發佈的簡單 Runbook 開始,同時瞭解如何跟蹤 Runbook 作業的狀態。 然後修改 Runbook 以實際管理 Azure 資源,在這種情況下啟動 Azure 虛擬機器。 通過添加 Runbook 參數和條件連結,完成本教程以使 Runbook 更加健壯。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md) ，用來保存 Runbook 以及向 Azure 資源驗證。 此帳戶必須擁有啟動和停止虛擬機器的權限。
* Azure 虛擬機器。 由於您停止並啟動此計算機,因此它不應是生產 VM。

## <a name="step-1---create-runbook"></a>步驟 1 - 建立 Runbook

首先建立簡單的的手冊,輸出文字`Hello World`。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。 

    [自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。 您應該已經有一些資產。 這些資產大多數是自動包含在新自動化帳戶中的模組。 您還應將憑據資產與您的訂閱相關聯。
2. 在 **「流程自動化**」下選擇**Runbook**以打開 Runbook 清單。
3. 通過選擇 **「創建執行簿」 建立新的 Runbook。**
4. 為 Runbook 提供名稱「MyFirstRunbook-Graphical」 ****。
5. 在這種情況下,您會建立一個[圖形執行簿](automation-graphical-authoring-intro.md)。 為**Runbook 類型**選擇**圖形**。<br> ![新的 Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. 按一下 [建立] **** 來建立 Runbook 並開啟圖形化編輯器。

## <a name="step-2---add-activities"></a>步驟 2 - 新增活動

編輯器左邊的 [程式庫] 控制項可讓您選取要加入到 Runbook 的活動。 您將新增 cmdlet`Write-Output`以從 Runbook 輸出文字。

1. 在「庫」控制項中,按下搜尋欄位並鍵入`write-output`。 搜索結果顯示在下圖中。 <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 向下捲動到清單底部。 右鍵按一**下 寫入輸出**並選擇「**新增到畫布**」 。。 或者,您可以按下 cmdlet 名稱旁邊的省略號 (...),然後選擇「**添加到畫布**」。
1. 按一下畫布上的 **Write-Output** 活動。 此動作會開啟可讓您設定活動的 [設定] 控制項頁面。
1. 「**標籤」** 欄位預設為 cmdlet 的名稱,但您可以將其更改為更友好的內容。 將改變為`Write Hello World to output`。
1. 按一下 [參數]**** 來提供 Cmdlet 的參數值。

   某些 cmdlet 具有多個參數集,您需要選擇要使用的參數集。 在這種情況下,`Write-Output`只有一個參數集。

1. 選擇參數`InputObject`。 這是用於指定要發送到輸出流的文本的參數。
1. **數據源**下拉功能表提供可用於填充參數值的源。 在此選單中, 選擇**PowerShell 表示式**。 

   您可以將來自此類源的輸出用作其他活動、自動化資產或 PowerShell 運算式。 在這種情況下,輸出只是`Hello World`。 您可以使用 PowerShell 運算式，並指定字串。<br>

1. 在 **「運算式」** 欄位中`Hello World`, 鍵入然後按下 **「確定**」兩次以返回到畫布。
1. 按一下 [儲存] **** 來儲存 Runbook。

## <a name="step-3---test-the-runbook"></a>步驟 3 - 測試 Runbook

在發佈 Runbook 使其在生產中可用之前,應對其進行測試以確保其正常工作。 測試 Runbook 會運行其草稿版本,並允許您以交互方式查看其輸出。

1. 選擇 **「測試」窗格**以開啟「測試」 窗格。
1. 按一下 [開始] **** 以開始測試。 這應該是唯一啟用的選項。
1. 請注意,將創建[Runbook 作業](automation-runbook-execution.md),其狀態將顯示在窗格中。

   作業狀態以`Queued`開始 ,指示作業正在等待雲中的 Runbook 工作人員變為可用。 狀態更改為`Starting`工作人員聲明作業時的狀態。 最後,當 Runbook 實際開始運行時,狀態`Running`變為 該狀態。

1. 運行簿作業完成後,"測試"窗格將顯示其輸出。 在這種情況下,您將看到`Hello World`。

    ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 關閉 [測試] 窗格以返回畫布。

## <a name="step-4---publish-and-start-the-runbook"></a>步驟 4 - 發佈和啟動 Runbook

您建立的 Runbook 仍處於草稿模式。 您需要將它發佈，才能在生產環境中執行它。 當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。 在此情況下，因為您剛剛建立 Runbook，所以還沒有已發佈的版本。

1. 選取 [發佈]**** 來發佈 Runbook，然後在系統提示時選取 [是]****。
1. 向左滾動以檢視 Runbook 頁上的 Runbook,並注意 **「創作狀態」** 值設定為 **「已發布**」 。。
1. 捲回右側，以檢視 **MyFirstRunbook-Graphical** 的頁面。

   頂部的選項允許您立即啟動 Runbook、安排將來的開始時間或創建[Webhook,](automation-webhooks.md)以便透過 HTTP 調用啟動 Runbook。

1. 選取 [開始]****，然後在系統提示時選取 [是]****，以啟動 Runbook。
1. 為已創建的 Runbook 作業打開作業窗格。 驗證**作業狀態**欄位是否顯示 **「已完成**」。
1. 按下 **「輸出**」以開啟「輸出」頁,您`Hello World`可以在其中顯示。
1. 關閉 [輸出] 頁面。
1. 按一下 [所有記錄] **** 以開啟 Runbook 作業的 [資料流] 窗格。 您應該只在輸出`Hello World`流中看到。 

    請注意,如果 Runbook 寫入 Runbook,則"流"窗格可以顯示 Runbook 作業的其他流,如"詳細"和"錯誤"流。
1. 關閉「流」窗格和作業窗格以返回到「MyFirstRunbook-圖形」 頁。
1. 要檢視 Runbook 的所有工作,請在 **「資源**」 選單**工作**。 "作業"頁列出了 Runbook 創建的所有作業。 您應該只看到一個列出的作業,因為您只運行該作業一次。
1. 按一下作業名稱可打開啟動 Runbook 時查看的相同作業窗格。 使用此窗格可以查看為 Runbook 創建的任何作業的詳細資訊。

## <a name="step-5---create-variable-assets"></a>步驟 5 - 建立變數資產

您已經測試併發佈了 Runbook,但到目前為止,它沒有任何有用的管理 Azure 資源。 在配置 Runbook 進行身份驗證之前,必須創建一個變數來保存訂閱 ID,設置要進行身份驗證的活動,然後引用該變數。 通過包含對訂閱上下文的引用,可以輕鬆地處理多個訂閱。

1. 從導航窗格上的 **「訂閱」** 選項複製訂閱 ID。
1. 在「自動化帳戶」 頁中,選擇 **「分享資源**」 的**變數**。
1. 選取 [新增變數]****。
1. 在"新建變數"頁上,在提供的欄位中進行以下設置。

    * **名稱**`AzureSubscriptionId`-- 輸入 。
    * **值**-- 輸入訂閱 ID。 
    * **類型**-- 保持字串選擇。
    * **加密**-- 使用預設值。
1. 按一下 [建立] **** 來建立變數。

## <a name="step-6---add-authentication"></a>步驟 6 - 新增身份驗證

現在,您擁有一個變數來保存訂閱 ID,則可以將 Runbook 配置為使用訂閱的"運行為"憑據進行身份驗證。 為此,將 Azure 運行為連接添加為資產。 您還必須將[連接-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) cmdlet 和[Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) cmdlet 添加到畫布中。

>[!NOTE]
>對於 PowerShell`Add-AzAccount``Add-AzureRMAccount`執行簿`Connect-AzAccount`,並且是 的別名。 請注意,這些別名不適用於圖形運行簿。 圖形運行簿只能使用`Connect-AzAccount`自身。

1. 導航到 Runbook,並在 MyFirstRunbook-圖形頁面上選擇 **「編輯**」。
1. 您不再需要該`Write Hello World to output`條目了。 只需按一下省略號並選擇 **「刪除**」。
1. 在函式庫控制項中,展開**ASSETS**,然後**展開連線**。 `AzureRunAsConnection`以選擇 **「新增到畫布」 新增到畫布**。
1. 將 `AzureRunAsConnection` 重新命名為 `Get Run As Connection`。
1. 在"庫"控制項中`Connect-AzAccount`,在搜索欄位中鍵入。
1. 添加到`Connect-AzAccount`畫布。
1. 在下點`Get Run As Connection`, 直到形狀底部出現一個圓圈。 單擊圓圈並拖動箭頭以`Connect-AzAccount`形成連結。 Runbook`Get Run As Connection`從開始,`Connect-AzAccount`然後執行 。<br> ![建立活動之間的連結](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 在畫布上,選擇`Connect-AzAccount`。 在"配置"控制件窗格中,在 **"標籤"** 欄位中鍵入 **「登錄到 Azure」。。**
1. 按下**參數**,將顯示"活動參數設定"頁。
1. `Connect-AzAccount` cmdlet 具有多個參數集,您需要在提供參數值之前選擇一個參數集。 點選**參數集**,然後選擇**有訂閱 ID 的服務主體憑證**。
1. 此參數集的參數顯示在「活動參數設定」頁上。 按一下 [APPLICATIONID]****。<br> ![新增 Azure 帳號參數](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 在「參數值」頁上,進行以下設置,然後單擊「**確定**」。

   * **資料來源**-- 選擇**作用 。**
   * 資料來源清單 ─ 選擇**自動化連線**。
   * **欄位路徑**`ApplicationId`- 類型 。 您正在指定欄位路徑的屬性的名稱,因為活動輸出具有多個屬性的物件。

1. 按下 **「證書」,** 在參數值頁上,進行以下設置,然後單擊「**確定**」。

    * **資料來源**-- 選擇**作用 。**
    * 資料來源清單 ─ 選擇**自動化連線**。
    * **欄位路徑**`CertificateThumbprint`- 類型 。
1. 按下 **「服務」** 在「參數值」 頁上,選擇**資料源**欄位的常量值;在「參數值」 頁上,選擇「**恒定值**」 欄位。按下選項 **"True"** 然後單擊 **「確定**」。
1. 按一下**TENANTID**,並在「參數值」頁上進行以下設置。 完成後,單擊 **「確定」** 兩次。

    * **資料來源**-- 選擇**作用 。** 
    * 資料來源清單 ─ 選擇**自動化連線**。
    * **欄位路徑**`TenantId`- 類型 。 
1. 在"庫"控制項中`Set-AzContext`,在搜索欄位中鍵入。
1. 添加到`Set-AzContext`畫布。
1. 在`Set-AzContext`畫布上選擇。 在"配置"控制件窗格中`Specify Subscription Id`,在 **"標籤"** 欄位中輸入。
1. 按一下 [參數]****，隨即會顯示 [Activity Parameter Configuration] \(活動參數設定) 頁面。
1. `Set-AzContext` cmdlet 具有多個參數集,您需要在提供參數值之前選擇一個參數集。 按下**參數集**,然後選擇 **「訂閱 Id」。。**
1. 此參數集的參數顯示在「活動參數設定」頁上。 點選**訂閱代碼**。
1. 在「參數值」頁上,為**資料源**欄位選擇**可變資產**,並從源清單中選擇**Azure 訂閱 Id。** 完成後,單擊 **「確定」** 兩次。
1. 在下點`Login to Azure`, 直到形狀底部出現一個圓圈。 點選圓圈並將箭頭拖曳到`Specify Subscription Id`。 此時,Runbook 應如下所示。

    ![Runbook 驗證組態](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>步驟 7 - 加入活動以啟動虛擬機器

現在,您必須添加活動`Start-AzVM`才能啟動虛擬機器。 您可以在 Azure 訂閱中選擇任何 VM,現在正在硬編碼其名稱到[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet 中。

1. 在"庫"控制項中`Start-Az`,在搜索欄位中鍵入。
2. 新增到`Start-AzVM`畫布,然後按下並拖動它在`Specify Subscription Id`下方 。
1. 在下點`Specify Subscription Id`, 直到形狀底部出現一個圓圈。 點選圓圈並將箭頭拖曳到`Start-AzVM`。
1. 選取 `Start-AzVM`。 按下 **「參數」 然後**單擊**參數集**以查看活動的集。
1. 選擇參數集選擇**資源群組名稱參數集名稱**。 欄位**資源組名稱**和**名稱**旁邊有感嘆號,以指示它們是必需的參數。 請注意,這兩個字段都希望使用字串值。
1. 選擇**名稱**。 為**資料來源**欄位選擇**PowerShell 表示式**。 對於用於啟動此 Runbook 的 VM,鍵入用雙引弧括起來的電腦名稱。 按一下 [確定]  。
1. 選取 [ResourceGroupName]****。 使用**資料源**欄位的值**PowerShell 表示式**,並鍵入用雙引弧括起來的資源組的名稱。 按一下 [確定]  。
1. 按下 **「測試」窗格**,以便可以測試 Runbook。
1. 按下 **「開始」** 以開始測試。 完成後,請確保 VM 已啟動。 此時,Runbook 應如下所示。

    ![Runbook 驗證組態](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>步驟 8 - 新增其他輸入參數

Runbook 目前在`Start-AzVM`為 cmdlet 指定的資源群組中啟動 VM。 如果在啟動 Runbook 時同時指定名稱和資源組,則 Runbook 將更有用。 讓我們向 Runbook 添加輸入參數以提供該功能。

1. 按下「MyFirstRunbook-圖形」 頁上**的「編輯」** 開啟圖形編輯器。
1. 選取 [輸入和輸出]****，然後選取 [新增輸入]****，來開啟 [Runbook Input Parameter] \(Runbook 輸入參數) 窗格。
1. 在提供的欄位中進行以下設置,然後按一下**確定**。
   * **名稱**`VMName`-- 指定 。
   * **類型**-- 保留字串設定。
   * **必修**- 將值更改為 **"是**"。
1. 建立第二個稱為`ResourceGroupName`必需輸入參數,然後按下 **「確定」** 以關閉「輸入和輸出」窗格。<br> ![Runbook 輸入參數](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. 選擇作用`Start-AzVM`,然後按下**參數**。
1. 將**名稱**的**資料源**欄位變更為**Runbook 輸入**。 然後選擇**VMName**。
1. 將**資源群組名稱**的**資料源**欄位更改為**Runbook 輸入**,然後選擇 **「資源群組名稱**」 。。<br> ![啟動-AzVM 參數](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. 儲存 Runbook 並開啟 [測試] 窗格。 您現在可以提供測試中使用的兩個輸入變數的值。
1. 關閉 [測試] 窗格。
1. 按一下 [發佈] **** 來發行新版本的 Runbook。
1. 停止以前啟動的 VM。
1. 按一下 [開始] **** 以啟動 Runbook。 鍵入要啟動的`VMName`VM`ResourceGroupName`的值和值。
1. 運行簿完成後,請確保 VM 已啟動。

## <a name="step-9---create-a-conditional-link"></a>步驟 9 - 建立條件式連結

您現在可以修改 Runbook,以便它僅在 VM 尚未啟動時嘗試啟動它。 為此,添加檢索 VM 實例級狀態的[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) cmdlet。 然後,您可以添加使用 PowerShell`Get Status`代碼段 調用的 PowerShell 工作流代碼模組,以確定 VM 狀態是正在運行還是停止。 僅當當前運行狀態停止`Get Status`時,`Start-AzVM`才會運行模組的條件連結。 在此過程結束時,runbook`Write-Output`使用 cmdlet 輸出消息來通知您 VM 是否已成功啟動。

1. 在圖形化編輯器中開啟 **MyFirstRunbook-Graphical**。
1. 按下此選項`Specify Subscription Id`,`Start-AzVM`然後 按 **「刪除**」,刪除和之間的連結。
1. 在"庫"控制項中`Get-Az`,在搜索欄位中鍵入。
1. 添加到`Get-AzVM`畫布。
1. 選擇`Get-AzVM`然後按下 **「參數集」** 以檢視 cmdlet 的集。 
1. 選取 [GetVirtualMachineInResourceGroupNameParamSet] **** 參數集。 **資源組名稱**和**名稱**欄位旁邊有感嘆號,指示它們指定所需的參數。 請注意,這兩個字段都希望使用字串值。
1. 在 **「名稱****」 的資料來源**下,選擇**Runbook 輸入**,然後選擇**VMName**。 按一下 [確定]  。
1. 在 **「資源群組名稱****」 的資料來源**「下,選擇**Runbook 輸入**」,然後選擇**資源群組名稱**。 按一下 [確定]  。
1. 在 **「狀態的資料來源**」 ,選擇 **「常量」值** **Status** ,選擇**True**。 按一下 [確定]  。
1. 從 創建`Specify Subscription Id``Get-AzVM`從到的連結。
1. 在「庫」控件中,展開**Runbook 控制件**並將**程式碼**添加到畫布。  
1. 從 創建`Get-AzVM``Code`從到的連結。  
1. 按下`Code`「設定」窗格中,將標籤更改為 **「獲取狀態**」 。
1. 選擇`Code`並顯示代碼編輯器頁面。  
1. 將以下代碼段貼上到編輯器頁中。

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

1. 從 創建`Get Status``Start-AzVM`從到的連結。

    ![程式碼模組的 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 選擇連結,並在"配置"窗格中將 **"將條件應用**到**是**"。 請注意,連結變為虛線,指示目標活動僅在條件解析為 true 時才運行。  
1. 對**條件表示式**,鍵`$ActivityOutput['Get Status'] -eq "Stopped"`入 。 `Start-AzVM`現在僅在停止 VM 時運行。
1. 在 [程式庫] 控制項中，展開 **Cmdlet**，然後展開 **Microsoft.PowerShell.Utility**。
1. 添加到`Write-Output`畫布兩次。
1. 對於第一`Write-Output`個控制項,按下 **「參數**」並將**標籤**值更改為 **「通知 VM 已啟動**」。。
1. 對**InputObject,** 將**資料源**變更為**PowerShell 表示式**,並在表示式`$VMName successfully started.`中鍵入 。
1. 在第`Write-Output`二個控制項上,按下 **「參數**」**群組值**變更為**通知 VM 啟動失敗**。
1. 對**InputObject,** 將**資料源**變更為**PowerShell 表示式**,並在表示式`$VMName could not start`中鍵入 。
1. 從`Start-AzVM`和`Notify VM Started`創建`Notify VM Start Failed`連結。
1. 選擇指向`Notify VM Started`的連結並更改 **"將條件應用**為 true"
1. 對**條件表示式**,鍵`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`入 。 僅當`Write-Output`VM 成功啟動時,此控件才會運行。
1. 選擇指向`Notify VM Start Failed`的連結並更改 **"將條件應用**為 true"
1. 對**條件表示式**欄位,鍵入`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 僅當`Write-Output`VM 未成功啟動時,此控件才運行。 Runbook 應類似於下圖。

    ![Write-Output 的 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. 儲存 Runbook 並開啟 [測試] 窗格。
1. 在 VM 停止時啟動 Runbook,電腦應啟動。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關圖形創作的更多資訊,請參閱[Azure 自動化 中的圖形創作](automation-graphical-authoring-intro.md)。
* 要開始使用 PowerShell 執行簿,請參閱[我的第一個 PowerShell 執行簿](automation-first-runbook-textual-powershell.md)。
* 要開始使用 PowerShell 工作流執行簿,請參閱[我的第一個 PowerShell 工作流執行簿](automation-first-runbook-textual.md)。
* 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。