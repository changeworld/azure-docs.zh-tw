---
title: Azure 自動化的 Runbook 和模組資源庫
description: 來自 Microsoft 和社群的 Runbook 和模組可供您在 Azure 自動化環境中安裝及使用。  本文介紹如何訪問這些資源並將 Runbook 貢獻給庫。
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535548"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure 自動化的 Runbook 和模組資源庫

您可以存取已由 Microsoft 和社群建置的案例，而不是在 Azure 自動化中建立您自己的 Runbook 和模組。 您可以在文稿中心庫中從 PowerShell 函式庫和[Python runbook](#use-python-runbooks)取得 PowerShell 手冊與[模組](#modules-in-powershell-gallery)。 您還可以通過共用[您開發的方案](#add-a-powershell-runbook-to-the-gallery),為社區做出貢獻。 

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 函式庫中的 Runbook

[PowerShell 庫](https://www.powershellgallery.com/packages)提供了來自 Microsoft 和社區的各種 Runbook,您可以將這些手冊導入到 Azure 自動化中。 要使用其中一個,請從庫中下載 Runbook,或者可以直接從庫或 Azure 門戶中的自動化帳戶導入 Runbook。

> [!NOTE]
> PowerShell 庫中不支援圖形 Runbook。

只能使用 Azure 門戶直接從 PowerShell 庫導入。 不能使用 PowerShell 執行此功能。

> [!NOTE]
> 您應該驗證從 PowerShell 庫獲取的任何 Runbook 的內容,並在生產環境中安裝和運行它們時要格外小心。

## <a name="modules-in-powershell-gallery"></a>PowerShell 資源庫中的模組

PowerShell 模組包含您可以在 Runbook 中使用的 Cmdlet，您可以安裝在 Azure 自動化中的現有模組可於 [PowerShell 資源庫](https://www.powershellgallery.com)取得。 您可以從 Azure 入口網站啟動此資源庫，然後直接安裝至 Azure 自動化。 您也可以手動下載及安裝。

## <a name="common-solutions-available-in-powershell-gallery"></a>PowerShell 函式庫中提供的常見解決方案

下面的清單包含一些提供常見方案解決方案的 Runbook。 有關 Azure 自動化開發團隊建立的 Runbook 的完整清單,請參考[Azure 自動化團隊設定檔](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

   * [更新模組在自動化到最新版本](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/)- 從 PowerShell 庫導入自動化帳戶中所有模組的最新版本。
   * [啟用 Azure 診斷](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/)- 配置 Azure 診斷和日誌分析以接收包含作業狀態和作業流的 Azure 自動化日誌。
   * [複製專案從AzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - 從 Windows Azure 虛擬機器複製遠端檔。
   * [複製專案從AzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - 將本地檔案複製到 Azure 虛擬機器。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 門戶從 Runbook 函式庫導入 PowerShell 執行簿

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 **「過程自動化**」 選擇**Runbook 函式庫**。
3. 選擇 **「源:電源殼庫**」。
4. 找出您想要的資源庫項目，並且選取以檢視其詳細資料。 您可以在左邊輸入發行者和類型的其他搜尋參數。

   ![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)

5. 按一下 [檢視來源專案] **** 以檢視 [TechNet 指令碼中心](https://gallery.technet.microsoft.com/)中的項目。
6. 要導入專案,請單擊它以查看其詳細資訊,然後單擊「**導入**」。。

   ![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)

7. 選擇性變更 Runbook 的名稱，然後按一下 [確定] **** 以匯入 Runbook。
8. Runbook 將顯示在"自動化**帳戶的 Runbook"** 選項卡上。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>新增 PowerShell 執行簿

Microsoft 鼓勵您將 Runbook 添加到 PowerShell 庫,您認為這些手冊對其他客戶非常有用。 「PowerShell 資源庫」接受 PowerShell 模組和 PowerShell 指令碼。 您可以透過[自動載到 PowerShell 函式庫](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)來加入 Runbook。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>使用 Azure 門戶從模組庫匯入模組

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [共用資源]**** 下選取 [模組]****，以開啟模組清單。
3. 從頁面頂端按一下 [瀏覽資源庫]****。

   ![模組資源庫](media/automation-runbook-gallery/modules-blade.png)

4. 在 [瀏覽資源庫] 頁面上，您可以依下列欄位來搜尋：

   * 模組名稱
   * Tags
   * 作者
   * Cmdlet/DSC 資源名稱

5. 尋找您感興趣的模組，並選取以檢視其詳細資料。

   當您向下切入到特定的模組時，您可以檢視更多的資訊。 此資訊包括返回 PowerShell 資源庫的連結、任何必要的相依性，以及該模組包含的所有 Cmdlet 或 DSC 資源。

   ![PowerShell 模組的詳細資料](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. 要將模組直接安裝到 Azure 自動化中,請單擊「**導入**」。
7. 在「匯入」窗格中,您可以看到要導入的模組的名稱。 如果已安裝所有相依性，[確定]**** 按鈕會啟動。 如果缺少相依性，您必須先匯入這些相依性後，才能匯入此模組。
8. 在「導入」窗格中,按一下「**確定」** 以導入模組。 當 Azure 自動化將模組匯入至您的帳戶時，它會擷取有關模組和 Cmdlet 的中繼資料。 此操作可能需要幾分鐘時間,因為需要提取每個活動。
9. 您會收到初始通知，表示正在部署模組，而完成時還會收到另一個通知。
10. 匯入模組之後，您可以看到可用的活動。 您可以在 Runbook 和 DSC 資源中使用模組資源。

> [!NOTE]
> Azure 自動化不支援僅支援 PowerShell 核心的模組，該模組無法匯入 Azure 入口網站中，或直接從 PowerShell 資源庫部署。

## <a name="use-python-runbooks"></a>使用 Python 手冊

[指令碼中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)中提供 Python Runbook。 您可以通過按一下 **「 上傳貢獻**」 將 Python runbook 貢獻到文本中心庫。 執行此動作時,請確保在上傳貢獻時加入標籤`Python`。

> [!NOTE]
> 要將內容上載到[腳本中心](https://gallery.technet.microsoft.com/scriptcenter),至少需要 100 點。

## <a name="request-a-runbook-or-module"></a>要求執行簿或模組

您可以將要求傳送至 [使用者心聲](https://feedback.azure.com/forums/246290-azure-automation/)。  如果您需要協助撰寫 Runbook 或有關於 PowerShell 的問題，請將問題張貼至我們的[論壇](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc) \(英文\)。

## <a name="next-steps"></a>後續步驟

* 要開始使用 Runbook,請參閱[在 Azure 自動化 中管理 Runbook。](manage-runbooks.md)
* 要瞭解使用 Runbook 的 PowerShell 工作流和 PowerShell 工作流之間的區別,請參閱[學習 PowerShell 工作流](automation-powershell-workflow.md)。
* 有關 PowerShell 的更多資訊(包括語言參考和學習模組),請參閱[PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
