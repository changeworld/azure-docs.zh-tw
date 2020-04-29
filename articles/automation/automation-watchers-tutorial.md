---
title: 在 Azure 自動化帳戶中建立監看員工作
description: 了解如何在 Azure 自動化帳戶中建立監看員工作，以監看在資料夾中建立的新檔案。
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617349"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>建立 Azure 自動化監看員工作，以追蹤本機電腦上的檔案變更

Azure 自動化會使用監看員工作，透過 PowerShell runbook 尋找事件和觸發程式動作。 監看員工作包含兩個部分：監看員和動作。 監看員 runbook 會在監看員工作中定義的間隔執行，並將資料輸出到動作 runbook。 

本教學課程將逐步引導您建立監看員工作，以監視目錄中何時新增了檔案。 您會了解如何：

> [!div class="checklist"]
> * 匯入監看員 Runbook
> * 建立自動化變數
> * 建立動作 Runbook
> * 建立監看員工作
> * 觸發監看員
> * 檢查輸出

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，需要有下列項目：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)以保存監看員和動作 Runbook，以及監看員工作。
* 執行監看員工作的[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)。
* PowerShell runbook。 監看員作業不支援 PowerShell 工作流程 runbook。

> [!NOTE]
> Azure 中國不支援監看員工作。

## <a name="import-a-watcher-runbook"></a>匯入監看員 Runbook

本教學課程會使用名為 **Watch-NewFile** 的監看員 Runbook 來尋找目錄中的新檔案。 監看員 Runbook 會擷取資料夾檔案的最後已知寫入時間，並查看比該浮水印還新的任何檔案。

您可以透過[PowerShell 資源庫](https://www.powershellgallery.com)來完成此匯入程式。

1. 流覽至[Watch-NewFile](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)的 [資源庫] 頁面。
2. 在 [ **Azure 自動化**] 索引標籤下，按一下 [**部署] 以 Azure 自動化**。

您也可以使用下列步驟，從入口網站將此 runbook 匯入到您的自動化帳戶。

1. 開啟自動化帳戶，然後按一下 [Runbook] 頁面。
2. 按一下 **[流覽資源庫]**。
3. 搜尋 [監看員**runbook**]，選取 [監看員 runbook] 以**尋找目錄中的新**檔案，然後按一下 [匯**入**]。
  ![從 UI 匯入自動化 Runbook](media/automation-watchers-tutorial/importsourcewatcher.png)
4. 提供 runbook 的名稱和描述，然後按一下 **[確定]** ，將 runbook 匯入您的自動化帳戶。
5. 選取 [編輯]****，然後按一下 [發佈]****。 出現提示時，按一下 **[是]** 以發佈 runbook。

## <a name="create-an-automation-variable"></a>建立自動化變數

[自動化變數](automation-variables.md)可用來儲存先前之 Runbook 從每個檔案所讀取並儲存的時間戳記。

1. 選取 [**共用資源**] 下的 [**變數**]，然後按一下 [ **+ 新增變數**]。
1. 在 [名稱] 中輸入 NewFileTimestamp。
1. 選取類型的 [日期時間]。
1. 按一下 [**建立**] 以建立自動化變數。

## <a name="create-an-action-runbook"></a>建立動作 Runbook

在監看員工作中，動作 Runbook 會用來處理從監看員 Runbook 傳來的資料。 您必須從[PowerShell 資源庫](https://www.powershellgallery.com)匯入名為**Process-NewFile**的預先定義動作 runbook。 

若要建立動作 runbook：

1. 流覽至[Process-NewFile](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)的 [資源庫] 頁面。
2. 在 [ **Azure 自動化**] 索引標籤下，按一下 [**部署] 以 Azure 自動化**。

您也可以從 Azure 入口網站將此 runbook 匯入到您的自動化帳戶：

1. 流覽至您的自動化帳戶，然後選取 [**進程自動化**] 底下的 [ **runbook** ]。
1. 按一下 **[流覽資源庫]**。
1. 搜尋 [監看員**動作**]，選取處理監看員**runbook 所觸發之事件的**監看員動作，然後按一下 [匯**入**]。
  ![從 UI 匯入動作 Runbook](media/automation-watchers-tutorial/importsourceaction.png)
1. 提供 runbook 的名稱和描述，然後按一下 **[確定]** ，將 runbook 匯入您的自動化帳戶。
1. 選取 [編輯]****，然後按一下 [發佈]****。 出現提示時，按一下 **[是]** 以發佈 runbook。

## <a name="create-a-watcher-task"></a>建立監看員工作

在此步驟中，您會設定監看員工作，其中會參考上一節中定義的監看員和動作 runbook。

1. 流覽至您的自動化帳戶，**然後選取 [** 程式**自動化**] 底下的監看員工作。
1. 選取 [監看員工作] 頁面，然後按一下 [ **+ 新增**監看員工作]。
1. 輸入**WatchMyFolder**作為名稱。

1. 選取 [**設定**監看員]，然後選擇**Watch-NewFile** runbook。

1. 為各個參數輸入下列值︰

   * **FOLDERPATH** -混合式 Runbook 背景工作角色上的資料夾，其中會建立新的檔案，例如**d:\examplefiles**。
   * **延伸**模組-設定的延伸模組。 保留空白以處理所有副檔名。
   * **遞迴**-遞迴作業。 將此值保留為預設值。
   * 回合**設定-執行**runbook 的設定。 挑選混合式背景工作角色。

1. 按一下 **[確定]**，然後**選取**以返回監看員頁面。
1. 選取 [**設定動作**]，然後選擇 [**處理常式-NewFile** ] runbook。
1. 為各個參數輸入下列值︰

   * **EVENTDATA** -事件資料。 保留空白。 資料會從監看員 Runbook 傳入。
   * 回合**設定-執行**runbook 的設定。 保留為 [Azure]，因為此 runbook 會在 Azure 自動化中執行。

1. 按一下 **[確定]**，然後**選取**以返回監看員頁面。
1. 按一下 **[確定]** 以建立監看員工作。

![從 UI 設定監看員動作](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>觸發監看員

您必須執行如下所述的測試，以確保監看員工作會如預期般運作。 

1. 遠端進入混合式 Runbook 背景工作角色。 
2. 開啟 **PowerShell** 並在資料夾中建立測試檔案。

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

下列範例會顯示預期的輸出。

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>檢查輸出

1. 流覽至您的自動化帳戶，**然後選取 [** 程式**自動化**] 底下的監看員工作。
1. 選取 [監看員工作**WatchMyFolder**]。
1. 按一下 [**資料流程**] 底下的 [ **View 監看員資料流程**]，以查看監看員是否已找到新檔案，並已啟動動作 runbook。
1. 若要查看動作 runbook 作業，請按一下 [ **View 監看員動作作業**]。 您可以選取每個工作來查看作業的詳細資料。

   ![UI 中的監看員動作作業](media/automation-watchers-tutorial/WatcherActionJobs.png)

您可以在下列範例中看到系統找到新檔案時的預期輸出：

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 匯入監看員 Runbook
> * 建立自動化變數
> * 建立動作 Runbook
> * 建立監看員工作
> * 觸發監看員
> * 檢查輸出

遵循此連結以深入了解如何編寫自己的 Runbook。

> [!div class="nextstepaction"]
> [我的第一個 PowerShell runbook](automation-first-runbook-textual-powershell.md)。

