---
title: Azure 自動化的 Runbook 和模組資源庫
description: 來自 Microsoft 和社群的 Runbook 和模組可供您在 Azure 自動化環境中安裝及使用。  本文說明如何存取這些資源，並將您的 runbook 提供給資源庫。
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535548"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure 自動化的 Runbook 和模組資源庫

您可以存取已由 Microsoft 和社群建置的案例，而不是在 Azure 自動化中建立您自己的 Runbook 和模組。 您可以從 [腳本中心資源庫] 的 PowerShell 資源庫和[Python runbook](#use-python-runbooks)取得 PowerShell runbook 和[模組](#modules-in-powershell-gallery)。 您也可以藉由共用[您開發的案例](#add-a-powershell-runbook-to-the-gallery)來參與該小組。 

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 資源庫中的 runbook

[PowerShell 資源庫](https://www.powershellgallery.com/packages)提供來自 Microsoft 的各種 runbook，以及您可以匯入 Azure 自動化的「社區」。 若要使用，請從資源庫下載 runbook，或者您可以直接從資源庫或從 Azure 入口網站中的自動化帳戶匯入 runbook。

> [!NOTE]
> PowerShell 資源庫中不支援圖形化 runbook。

您只能使用 Azure 入口網站直接從 PowerShell 資源庫匯入。 您無法使用 PowerShell 來執行此功能。

> [!NOTE]
> 您應該驗證從 PowerShell 資源庫取得的任何 runbook 的內容，並且在生產環境中安裝和執行它們時特別小心。

## <a name="modules-in-powershell-gallery"></a>PowerShell 資源庫中的模組

PowerShell 模組包含您可以在 Runbook 中使用的 Cmdlet，您可以安裝在 Azure 自動化中的現有模組可於 [PowerShell 資源庫](https://www.powershellgallery.com)取得。 您可以從 Azure 入口網站啟動此資源庫，然後直接安裝至 Azure 自動化。 您也可以手動下載及安裝。

## <a name="common-solutions-available-in-powershell-gallery"></a>PowerShell 資源庫提供的常見解決方案

下列清單包含一些 runbook，可提供常見案例的解決方案。 如需 Azure 自動化小組所建立之 runbook 的完整清單，請參閱[AzureAutomationTeam profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

   * [Update-modulesinautomationtolatestversion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -從 PowerShell 資源庫匯入自動化帳戶中所有模組的最新版本。
   * [AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) -設定 Azure 診斷和 Log Analytics，以接收包含作業狀態和作業串流 Azure 自動化記錄。
   * [複製-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -從 Windows Azure 虛擬機器複製遠端檔案。
   * [複製-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) -將本機檔案複製到 Azure 虛擬機器。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 入口網站從 runbook 資源庫匯入 PowerShell runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 選取 [**流程自動化**] 底下的 [ **runbook 資源庫**]。
3. 選取**來源： PowerShell 資源庫**。
4. 找出您想要的資源庫項目，並且選取以檢視其詳細資料。 您可以在左邊輸入發行者和類型的其他搜尋參數。

   ![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)

5. 按一下 [檢視來源專案] **** 以檢視 [TechNet 指令碼中心](https://gallery.technet.microsoft.com/)中的項目。
6. 若要匯入專案，請按一下它以查看其詳細資料，然後按一下 [匯**入**]。

   ![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)

7. 選擇性變更 Runbook 的名稱，然後按一下 [確定] **** 以匯入 Runbook。
8. Runbook 會出現在自動化帳戶的 [ **runbook** ] 索引標籤上。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>將 PowerShell runbook 新增至資源庫

Microsoft 鼓勵您將 runbook 新增至您認為對其他客戶很有用的 PowerShell 資源庫。 「PowerShell 資源庫」接受 PowerShell 模組和 PowerShell 指令碼。 您可以藉由將 runbook[上傳至 PowerShell 資源庫](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)來新增它。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>使用 Azure 入口網站從模組庫匯入模組

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

6. 若要將模組直接安裝到 Azure 自動化中，請按一下 [匯**入**]。
7. 在 [匯入] 窗格中，您可以看到要匯入的模組名稱。 如果已安裝所有相依性，[確定]**** 按鈕會啟動。 如果缺少相依性，您必須先匯入這些相依性後，才能匯入此模組。
8. 在 [匯入] 窗格上，按一下 **[確定]** 以匯入模組。 當 Azure 自動化將模組匯入至您的帳戶時，它會擷取有關模組和 Cmdlet 的中繼資料。 此動作可能需要幾分鐘的時間，因為每個活動都需要解壓縮。
9. 您會收到初始通知，表示正在部署模組，而完成時還會收到另一個通知。
10. 匯入模組之後，您可以看到可用的活動。 您可以使用 runbook 和 DSC 資源中的模組資源。

> [!NOTE]
> Azure 自動化不支援僅支援 PowerShell 核心的模組，該模組無法匯入 Azure 入口網站中，或直接從 PowerShell 資源庫部署。

## <a name="use-python-runbooks"></a>使用 Python runbook

[指令碼中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)中提供 Python Runbook。 您可以按一下 **[上傳內容**]，將 Python runbook 提供給腳本中心資源庫。 當您這麼做時，請務必在上`Python`傳您的投稿時新增標記。

> [!NOTE]
> 若要將內容上傳至[腳本中心](https://gallery.technet.microsoft.com/scriptcenter)，您需要至少100點。

## <a name="request-a-runbook-or-module"></a>要求 runbook 或模組

您可以將要求傳送至 [使用者心聲](https://feedback.azure.com/forums/246290-azure-automation/)。  如果您需要協助撰寫 Runbook 或有關於 PowerShell 的問題，請將問題張貼至我們的[論壇](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc) \(英文\)。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 runbook，請參閱[Azure 自動化中的管理 runbook](manage-runbooks.md)。
* 若要瞭解使用 runbook 的 PowerShell 和 PowerShell 工作流程之間的差異，請參閱[瞭解 powershell 工作流程](automation-powershell-workflow.md)。
* 如需 PowerShell 的詳細資訊 (包括語言參考和學習模組)，請參閱 [PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
