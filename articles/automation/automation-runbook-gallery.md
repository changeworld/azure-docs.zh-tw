---
title: Azure 自動化的 Runbook 和模組資源庫
description: 來自 Microsoft 和社群的 Runbook 和模組可供您在 Azure 自動化環境中安裝及使用。  本文說明如何存取這些資源以及將您的 Runbook 貢獻至資源庫。
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421493"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure 自動化的 Runbook 和模組資源庫

您可以存取已由 Microsoft 和社群建置的案例，而不是在 Azure 自動化中建立您自己的 Runbook 和模組。

您可以在腳本中心庫中從 PowerShell 庫和[Python runbook](#python-runbooks)獲取 PowerShell 手冊和[模組](#modules-in-powershell-gallery)。 您也可以藉由共用您開發的案例來參與社群，請參閱將 Runbook 新增至資源庫

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 庫中的 Runbook

[PowerShell 庫](https://www.powershellgallery.com/packages)提供了來自 Microsoft 和社區的各種 Runbook，您可以將這些手冊導入到 Azure 自動化中。 要使用其中一個，請從庫中下載 Runbook，或者可以直接從庫或 Azure 門戶中的自動化帳戶導入 Runbook。

只能使用 Azure 門戶直接從 PowerShell 庫導入。 不能使用 PowerShell 執行此功能。

> [!NOTE]
> 您應該驗證從 PowerShell 庫獲取的任何 Runbook 的內容，並在生產環境中安裝和運行它們時要格外小心。

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 門戶從 Runbook 庫導入 PowerShell 運行簿

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [程序自動化]**** 底下，按一下 [Runbook 資源庫]****
3. 選擇 **"源：電源殼庫**"。
4. 找出您想要的資源庫項目，並且選取以檢視其詳細資料。 您可以在左邊輸入發行者和類型的其他搜尋參數。

   ![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)

5. 按一下 [檢視來源專案] **** 以檢視 [TechNet 指令碼中心](https://gallery.technet.microsoft.com/)中的項目。
6. 若要匯入項目，請按一下以檢視其詳細資料，然後按一下 [匯入] **** 按鈕。

   ![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)

7. 選擇性變更 Runbook 的名稱，然後按一下 [確定] **** 以匯入 Runbook。
8. Runbook 會出現在自動化帳戶的 [Runbook]**** 索引標籤上。

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>向庫添加 PowerShell 運行簿

Microsoft 鼓勵您將 Runbook 添加到 PowerShell 庫，您認為這些手冊對其他客戶非常有用。 「PowerShell 資源庫」接受 PowerShell 模組和 PowerShell 指令碼。 您可以通過[將其上載到 PowerShell 庫](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)來添加 Runbook。

> [!NOTE]
> PowerShell 庫中不支援圖形 Runbook。

## <a name="modules-in-powershell-gallery"></a>PowerShell 資源庫中的模組

PowerShell 模組包含您可以在 Runbook 中使用的 Cmdlet，您可以安裝在 Azure 自動化中的現有模組可於 [PowerShell 資源庫](https://www.powershellgallery.com)取得。 您可以從 Azure 入口網站啟動此資源庫，然後直接安裝至 Azure 自動化。 您也可以手動下載及安裝。

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>透過 Azure 入口網站從自動化模組資源庫匯入模組

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [共用資源]**** 下選取 [模組]****，以開啟模組清單。
3. 從頁面頂端按一下 [瀏覽資源庫]****。

   ![模組資源庫](media/automation-runbook-gallery/modules-blade.png)

4. 在 [瀏覽資源庫]**** 頁面上，您可以依下列欄位來搜尋：

   * 模組名稱
   * Tags
   * 作者
   * Cmdlet/DSC 資源名稱

5. 尋找您感興趣的模組，並選取以檢視其詳細資料。

   當您向下切入到特定的模組時，您可以檢視更多的資訊。 此資訊包括返回 PowerShell 資源庫的連結、任何必要的相依性，以及該模組包含的所有 Cmdlet 或 DSC 資源。

   ![PowerShell 模組的詳細資料](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. 若要將該模組直接安裝至 Azure 自動化，請按一下 [匯入] **** 按鈕。
7. 按一下 [匯入] 按鈕時，在 [匯入]**** 窗格上，您會看到將要匯入的模組名稱。 如果已安裝所有相依性，[確定]**** 按鈕會啟動。 如果缺少相依性，您必須先匯入這些相依性後，才能匯入此模組。
8. 在 [匯入]**** 分頁上按一下 [確定]**** 以匯入模組。 當 Azure 自動化將模組匯入至您的帳戶時，它會擷取有關模組和 Cmdlet 的中繼資料。 因為必須解壓縮每個活動，此動作可能需要幾分鐘的時間。
9. 您會收到初始通知，表示正在部署模組，而完成時還會收到另一個通知。
10. 匯入模組之後，您可以看到可用的活動。 您可以將其資源用於 Runbook 和「預期狀態設定」中。

> [!NOTE]
> Azure 自動化不支援僅支援 PowerShell 核心的模組，該模組無法匯入 Azure 入口網站中，或直接從 PowerShell 資源庫部署。

## <a name="python-runbooks"></a>Python Runbook

[指令碼中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)中提供 Python Runbook。 您可以通過按一下"**上傳貢獻**"將 Python runbook 貢獻到腳本中心庫。 當您這麼做時，請務必在上傳您的貢獻項目時新增標籤 **Python**。

> [!NOTE]
> 要將內容上載到[腳本中心](https://gallery.technet.microsoft.com/scriptcenter)，至少需要 100 點。

## <a name="requesting-a-runbook-or-module"></a>要求 Runbook 或模組

您可以將要求傳送至 [使用者心聲](https://feedback.azure.com/forums/246290-azure-automation/)。  如果您需要協助撰寫 Runbook 或有關於 PowerShell 的問題，請將問題張貼至我們的[論壇](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc) \(英文\)。

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Runbook 庫中提供的常見解決方案

下面的清單包含一些提供常見方案解決方案的 Runbook。 有關 Azure 自動化團隊創建的 Runbook 的完整清單，請參閱[Azure 自動化團隊設定檔](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

* [更新模組在自動化到最新版本](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/)- 在 PowerShell 庫中導入自動化帳戶中所有模組的最新版本。
* [啟用 Azure 診斷](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/)- 此腳本配置 Azure 診斷和日誌分析以接收包含作業狀態和作業流的 Azure 自動化日誌。
* [複製專案從AzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - 此運行簿從 Windows Azure 虛擬機器複製遠端檔。
* [複製專案從AzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - 此運行簿將本地檔案複製到 Azure 虛擬機器。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 Runbook，請參閱[在 Azure 自動化中管理 Runbook](manage-runbooks.md)
* 若要了解含有 Runbook 的 PowerShell 和 PowerShell 工作流程之間的差異，請參閱 [了解 PowerShell 工作流程](automation-powershell-workflow.md)
* 有關 PowerShell 的更多資訊（包括語言參考和學習模組），請參閱[PowerShell 文檔](https://docs.microsoft.com/powershell/scripting/overview)。
