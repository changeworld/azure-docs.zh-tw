---
title: 使用 PowerShell 資源庫中的 Azure 自動化 Runbook 和模組
description: 本文說明如何使用 PowerShell 資源庫中 Microsoft 和社群的 Runbook 和模組。
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: f2bf058ddce81ab9f04e97787a4dc93e44036b1b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186107"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>使用 PowerShell 資源庫中的 Runbook 和模組

您可以存取已由 Microsoft 和社群建置的案例，而不是在 Azure 自動化中建立您自己的 Runbook 和模組。 您可以從 PowerShell 資源庫取得 PowerShell Runbook 和[模組](#modules-in-powershell-gallery)，並從指令碼中心資源庫取得 [Python Runbook](#use-python-runbooks)。 您也可以藉由共用[您開發的案例](#add-a-powershell-runbook-to-the-gallery)來參與社群。 

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 資源庫中的 Runbook

[PowerShell 資源庫](https://www.powershellgallery.com/packages)提供各種來自 Microsoft 和社群的 Runbook，您可以將其匯入 Azure 自動化。 若要使用 Runbook，請從資源庫下載 Runbook，或者，您可以直接從資源庫匯入 Runbook， 或從 Azure 入口網站的自動化帳戶匯入。

> [!NOTE]
> PowerShell 資源庫中不支援圖形化 Runbook。

若要直接從 PowerShell 資源庫匯入，您只能使用 Azure 入口網站。 您無法使用 PowerShell 執行此函式。

> [!NOTE]
> 您應該驗證從 PowerShell 資源庫取得的任何 Runbook 的內容，並且在實際執行環境中安裝和執行這些內容時請小心謹慎。

## <a name="modules-in-powershell-gallery"></a>PowerShell 資源庫中的模組

PowerShell 模組包含您可以在 Runbook 中使用的 Cmdlet，您可以安裝在 Azure 自動化中的現有模組可於 [PowerShell 資源庫](https://www.powershellgallery.com)取得。 您可以從 Azure 入口網站啟動此資源庫，然後直接安裝至 Azure 自動化。 您也可以手動下載及安裝。

## <a name="common-scenarios-available-in-powershell-gallery"></a>PowerShell 資源庫提供的常見案例

下列清單包含一些支援常見案例的 Runbook。 如需 Azure 自動化小組建立的 Runbook 完整清單，請參閱 [AzureAutomationTeam 設定檔](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - 從 PowerShell 資源庫匯入自動化帳戶中最新版本的所有模組。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - 設定 Azure 診斷和記錄分析，以接收包含作業狀態和作業資料流的 Azure 自動化記錄。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - 從 Windows Azure 虛擬機器複製遠端檔案。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - 將本機檔案複製到 Azure 虛擬機器。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 入口網站從 Runbook 資源庫匯入 PowerShell Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [程序自動化] 下，選取 [Runbook 資源庫]。
3. 選取 [來源：PowerShell 資源庫]。
4. 找出您想要的資源庫項目，並且選取以檢視其詳細資料。 您可以在左邊輸入發行者和類型的其他搜尋參數。

   ![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)

5. 按一下 [檢視來源專案]  以檢視 [TechNet 指令碼中心](https://gallery.technet.microsoft.com/)中的項目。
6. 若要匯入項目，請按一下此選項以檢視其詳細資料，然後按一下 [匯入]。

   ![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)

7. 選擇性變更 Runbook 的名稱，然後按一下 [確定]  以匯入 Runbook。
8. Runbook 會出現在自動化帳戶的 [Runbook] 索引標籤上。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>將 PowerShell runbook 新增至資源庫

Microsoft 鼓勵您將 Runbook 新增至您認為可能有助於其他客戶的 PowerShell 資源庫。 「PowerShell 資源庫」接受 PowerShell 模組和 PowerShell 指令碼。 您可以藉由[將其上傳至 PowerShell 資源庫](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)，以新增 Runbook。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>透過 Azure 入口網站從模組資源庫匯入模組

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [共用資源] 下選取 [模組]，以開啟模組清單。
3. 從頁面頂端按一下 [瀏覽資源庫]。

   ![模組資源庫](media/automation-runbook-gallery/modules-blade.png)

4. 在 [瀏覽資源庫] 頁面上，您可以依下列欄位來搜尋：

   * 模組名稱
   * Tags
   * 作者
   * Cmdlet/DSC 資源名稱

5. 尋找您感興趣的模組，並選取以檢視其詳細資料。

   當您向下切入到特定的模組時，您可以檢視更多的資訊。 此資訊包括返回 PowerShell 資源庫的連結、任何必要的相依性，以及該模組包含的所有 Cmdlet 或 DSC 資源。

   ![PowerShell 模組的詳細資料](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. 若要將該模組直接安裝至 Azure 自動化，請按一下 [匯入]。
7. 在 [匯入] 窗格中，您可以看見要匯入的模組名稱。 如果已安裝所有相依性，[確定] 按鈕會啟動。 如果缺少相依性，您必須先匯入這些相依性後，才能匯入此模組。
8. 在 [匯入] 窗格中，按一下 [確定]，以匯入模組。 當 Azure 自動化將模組匯入至您的帳戶時，它會擷取有關模組和 Cmdlet 的中繼資料。 因為必須解壓縮每個活動，此動作可能需要幾分鐘的時間。
9. 您會收到初始通知，表示正在部署模組，而完成時還會收到另一個通知。
10. 匯入模組之後，您可以看到可用的活動。 您可以使用 Runbook 和 DSC 資源中的模組資源。

> [!NOTE]
> Azure 自動化不支援僅支援 PowerShell 核心的模組，該模組無法匯入 Azure 入口網站中，或直接從 PowerShell 資源庫部署。

## <a name="use-python-runbooks"></a>使用 Python Runbook

[指令碼中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)中提供 Python Runbook。 您可以按一下 [上傳貢獻]，將 Python Runbook 提供給指令碼中心資源庫。 當您這麼做時，請務必在上傳您的貢獻項目時，新增標籤 `Python`。

> [!NOTE]
> 若要將內容上傳至[指令碼中心](https://gallery.technet.microsoft.com/scriptcenter)，您至少需要 100 點。

## <a name="request-a-runbook-or-module"></a>要求 Runbook 或模組

您可以將要求傳送至 [使用者心聲](https://feedback.azure.com/forums/246290-azure-automation/)。  如果您需要協助撰寫 Runbook 或有關於 PowerShell 的問題，請將問題張貼至我們的 [Microsoft 問與答頁面](/answers/topics/azure-automation.html) \(英文\)。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 PowerShell Runbook，請參閱[教學課程：建立 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要使用 Runbook，請參閱[在 Azure 自動化中管理 Runbook](manage-runbooks.md)。
* 如需 PowerShell 的詳細資料，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
* * 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
