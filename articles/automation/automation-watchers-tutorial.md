---
title: 在 Azure 自動化帳戶中建立監看員工作
description: 了解如何在 Azure 自動化帳戶中建立監看員工作，以監看在資料夾中建立的新檔案。
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617349"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>建立 Azure 自動化監看員工作，以追蹤本機電腦上的檔案變更

Azure 自動化使用觀察程式任務使用 PowerShell Runbook 查找事件並觸發操作。 觀察程式任務包含兩個部分,觀察器和操作。 觀察程式執行簿以觀察程式任務中定義的間隔運行,並將數據輸出到操作 Runbook。 

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
* 電源外殼運行簿。 觀察程式任務不支援 PowerShell 工作流執行簿。

> [!NOTE]
> Azure 中國不支持觀察程式任務。

## <a name="import-a-watcher-runbook"></a>匯入監看員 Runbook

本教學課程會使用名為 **Watch-NewFile** 的監看員 Runbook 來尋找目錄中的新檔案。 監看員 Runbook 會擷取資料夾檔案的最後已知寫入時間，並查看比該浮水印還新的任何檔案。

此導入過程可以通過[PowerShell 庫](https://www.powershellgallery.com)完成。

1. 導航到[「觀看-新檔.ps1」](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)的庫頁面。
2. 在**Azure 自動化**選項卡下,按一下「**部署到 Azure 自動化**」。

您還可以使用以下步驟從門戶將此 Runbook 導入自動化帳戶。

1. 開啟自動化帳戶，然後按一下 [Runbook] 頁面。
2. 按下 **「流覽庫**」。
3. 搜尋**啟動程式執行簿**,選擇**在目錄中尋找新檔案的觀察程式執行簿**,然後按下「**匯入**」。
  ![從 UI 匯入自動化 Runbook](media/automation-watchers-tutorial/importsourcewatcher.png)
4. 為 Runbook 指定名稱和說明,然後按一下 **「 確定」** 將 Runbook 導入自動化帳戶。
5. 選取 [編輯]****，然後按一下 [發佈]****。 當出現提示時,按一下「**以**發佈 Runbook。

## <a name="create-an-automation-variable"></a>建立自動化變數

[自動化變數](automation-variables.md)可用來儲存先前之 Runbook 從每個檔案所讀取並儲存的時間戳記。

1. 在 **"分享資源**"下選擇**變數**,然後按一**下 '添加變數**"。
1. 輸入名稱的「觀看-新文件時間」 戳。
1. 選擇類型的日期時間。
1. 按下 **「創建**」以創建自動化變數。

## <a name="create-an-action-runbook"></a>建立動作 Runbook

在監看員工作中，動作 Runbook 會用來處理從監看員 Runbook 傳來的資料。 您必須從[PowerShell 函式庫](https://www.powershellgallery.com)匯入名為 **'行程-新檔案**'的預定義操作執行簿。 

要創建操作運行簿,

1. 導航到[進程-新檔.ps1 的](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)庫頁面。
2. 在**Azure 自動化**選項卡下,按一下「**部署到 Azure 自動化**」。

還可以從 Azure 門戶將此 Runbook 匯入自動化帳戶:

1. 導航到您的自動化帳戶,並在 **「流程自動化**」下選擇**Runbook。**
1. 按下 **「流覽庫**」。
1. 搜尋**啟動程式操作**,選擇**處理由觀察程式執行簿觸發的事件的觀察程式操作**,然後按下「**匯入**」。
  ![從 UI 匯入動作 Runbook](media/automation-watchers-tutorial/importsourceaction.png)
1. 為 Runbook 指定名稱和說明,然後按一下 **「 確定」** 將 Runbook 導入自動化帳戶。
1. 選取 [編輯]****，然後按一下 [發佈]****。 當出現提示時,按一下「**以**發佈 Runbook。

## <a name="create-a-watcher-task"></a>建立監看員工作

在此步驟中,配置觀察程式任務,引用前面各節中定義的觀察程式和操作運行簿。

1. 導覽您的自動化帳戶,並在 **「過程自動化**」下選擇**觀察程式工作**。
1. 選擇「觀察程式工作」 頁,然後按**下 「新增觀察程式」 的工作**。
1. 輸入 **「監視我的資料夾**」作為名稱。

1. 選擇 **「設定觀察程式**」並選擇 **「監視-新檔案**」 執行簿。

1. 為各個參數輸入下列值︰

   * **FOLDERPATH** - 混合 Runbook 工作線程式的資料夾,其中建立新檔案,例如**d:\範例檔**。
   * **擴展**- 配置的擴展。 保留空白以處理所有副檔名。
   * **RECURSE** - 遞迴操作。 保留此值作為預設值。
   * **執行設定**- 用於運行 Runbook 的設定。 挑選混合式背景工作角色。

1. 按下 **「確定**」,然後**選擇**返回到「觀察程式」 頁。
1. 選擇 **「設定作業**」並選擇 **「行程-新檔案**」 執行簿。
1. 為各個參數輸入下列值︰

   * **事件資料**─ 事件資料。 保留空白。 資料會從監看員 Runbook 傳入。
   * **執行設定**- 用於運行 Runbook 的設定。 離開 Azure,因為此 Runbook 在 Azure 自動化中運行。

1. 按下 **「確定**」,然後**選擇**返回到「觀察程式」 頁。
1. 按下「**確定」** 以創建觀察程式任務。

![從 UI 設定監看員動作](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>觸發監看員

您必須運行如下所述的測試,以確保觀察程式任務按預期工作。 

1. 遠端到混合 Runbook 工作線程。 
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

1. 導覽您的自動化帳戶,並在 **「過程自動化**」下選擇**觀察程式工作**。
1. 選擇觀察程式任務**WatchMyFolder**。
1. 按下 **「流**」下的 **「查看觀察程式流**」以查看觀察程式已找到新檔並啟動操作執行簿。
1. 要查看操作執行簿作業,請按下 **「查看觀察程式操作作業**」。。 可以選擇每個作業以查看作業的詳細資訊。

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
> [我的第一個PowerShell執行簿](automation-first-runbook-textual-powershell.md)。

