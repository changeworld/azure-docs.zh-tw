---
title: 建立獨立的 Azure 自動化帳戶
description: 本文會逐步引導您在 Azure 自動化中建立、測試和使用範例安全性主體驗證。
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: f06480767b697dca8fe41e484c02aefc58f040bf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261324"
---
# <a name="create-a-standalone-azure-automation-account"></a>建立獨立的 Azure 自動化帳戶

本文示範如何在 Azure 入口網站中建立 Azure 自動化帳戶。 您可以使用門戶自動化帳戶來評估和學習自動化,而無需使用其他管理解決方案或與 Azure 監視器日誌集成。 您可以添加這些管理解決方案或與 Azure 監視器日誌整合,以便將來的任何時候對 Runbook 作業進行進階監視。

使用自動化帳戶，您可以藉由在 Azure Resource Manager 或傳統部署模型中管理資源來驗證 Runbook。 一個「自動化帳戶」可以管理所指定租用戶之所有區域和訂用帳戶的資源。

當您在 Azure 入口網站中建立自動化帳戶時，即會自動建立這些帳戶：

* **以帳號身份執行**。 此帳戶會執行下列工作：
  * 在 Azure Active Directory (Azure AD) 中建立服務主體。
  * 建立憑證。
  * 指派參與者角色型存取控制 (RBAC)，此控制可使用 Runbook 來管理 Azure Resource Manager 資源。

利用這些為您建立的帳戶，您可以快速開始建置和部署 Runbook 以支援您的自動化需求。

## <a name="permissions-required-to-create-an-automation-account"></a>建立自動化帳戶所需的權限

若要建立或更新自動化帳戶，以及完成本文中所述的工作，您必須具有下列權限：

* 要建立自動化帳戶,必須將 Azure AD 使用者帳戶新增到具有與 Microsoft 擁有者角色等值的許可權的角色 **。自動化**資源。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 在 Azure 門戶中,在**Azure 的目錄** > **管理** > **使用者設定**下,如果**應用程式**設定為 **「是」,** 則 Azure AD 租戶中的非管理員使用者可以[註冊活動目錄應用程式](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions)。 如果將 [應用程式註冊]**** 設為 [否]****，則執行此動作的使用者必須是 Azure AD 中的全域管理員。

如果在添加到訂閱的全域管理員/共同管理員角色之前,您不是訂閱的 Active Directory 實例的成員,則作為來賓將添加到 Active Directory。 在這種情況下,您可以在 **「添加自動化帳戶」** 頁上看到此消息:「您沒有建立許可權。

如果先將使用者加入至全域管理員/共同管理員角色，您可以從訂用帳戶的 Active Directory 執行個體中移除他們，然後將他們重新加入至 Active Directory 中的完整使用者角色。

驗證使用者角色：

1. 在 Azure 入口網站中，移至 [Azure Active Directory]**** 窗格。
1. 選擇**使用者與群組**。
1. 選擇**所有使用者**。
1. 當您選取特定的使用者之後，選取 [設定檔]****。 使用者設定檔下方的 [使用者類型]**** 屬性值不應為 [來賓]****。

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>在 Azure 入口網站中建立新的自動化帳戶

若要在 Azure 入口網站中建立 Azure 自動化帳戶，請完成下列步驟：

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
1. 選取 [+ 建立資源]****。
1. 搜尋 [自動化]****。 在搜尋結果中，選取 [自動化]****。

   ![在 Azure Marketplace 中搜尋「自動化與控制」並加以選取](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. 在下一個螢幕上選擇 **"創建**"

   ![加入自動化帳戶](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > 如果您在 [加入自動化帳戶]**** 窗格中看見下列訊息，您的帳戶就不是訂用帳戶管理員角色的成員和訂用帳戶的共同管理員。
   >
   > ![加入自動化帳戶警告](media/automation-create-standalone-account/create-account-without-perms.png)

1. 在 [加入自動化帳戶]**** 窗格的 [名稱]**** 方塊中，輸入新自動化帳戶的名稱。 選擇名稱之後，就無法變更此名稱。 *自動化帳戶名稱對於每個區域和資源組是唯一的。已刪除的自動化帳戶的名稱可能不會立即可用。*
1. 如果您有多個訂用帳戶，請在 [訂用帳戶]**** 方塊中，指定您想要用於新帳戶的訂用帳戶。
1. 對於 [資源群組]****，輸入或選取新的或現有的資源群組。
1. 對於 [位置]****，選取一個 Azure 資料中心位置。
1. 對於 [建立 Azure 執行身分帳戶]**** 選項，確定已選取 [是]****，然後選取 [建立]****。

   > [!NOTE]
   > 如果您針對 [建立 Azure 執行身分帳戶]**** 選取 [否]****，以選擇不要建立執行身分帳戶，則 [加入自動化帳戶]**** 窗格中會顯示一則訊息。 雖然此帳戶建立於 Azure 入口網站中，但此帳戶在傳統部署模型訂用帳戶或 Azure Resource Manager 訂用帳戶目錄服務內不會有對應的驗證身分識別。 因此，自動化帳戶無法存取您訂用帳戶中的資源。 這會導致所有參考此帳戶的 Runbook 均無法進行驗證，也無法對那些部署模型中的資源執行工作。
   >
   > ![加入自動化帳戶警告](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > 若未建立服務主體，則不會指派參與者角色。
   >

1. 若要追蹤自動化帳戶建立的進度，請在功能表中選取 [通知]****。

### <a name="resources-included"></a>包含的資源

順利建立自動化帳戶時，系統會自動為您建立幾項資源。 建立之後，如果您不想保留這些 Runbook，可以安全地全部刪除。 「執行身分帳戶」可以用來在 Runbook 中驗證您的帳戶，應該保留，除非您要另外建立一個或不需要它們。 下表摘要說明執行身分帳戶的資源。

| 資源 | 描述 |
| --- | --- |
| AzureAutomationTutorial Runbook |此為圖形化 Runbook 範例，示範如何使用執行身分帳戶進行驗證。 Runbook 會取得所有 Resource Manager 資源。 |
| AzureAutomationTutorialScript Runbook |此為 PowerShell Runbook 範例，示範如何使用執行身分帳戶進行驗證。 Runbook 會取得所有 Resource Manager 資源。 |
| AzureAutomationTutorialPython2 Runbook |此為 Python Runbook 範例，示範如何使用執行身分帳戶進行驗證。 Runbook 會列出所有存在於訂用帳戶中的資源群組。 |
| AzureRunAsCertificate |在建立自動化帳戶時自動建立，或針對現有帳戶使用 PowerShell 指令碼建立的憑證資產。 此憑證會向 Azure 進行驗證，讓您可以從 Runbook 管理 Azure Resource Manager 資源。 此憑證有一年的有效期。 |
| AzureRunAsConnection |在建立自動化帳戶時自動建立，或針對現有帳戶使用 PowerShell 指令碼建立的連線資產。 |

## <a name="create-a-classic-run-as-account"></a>建立經典執行身份帳戶

默認情況下,在創建 Azure 自動化帳戶時,將不再創建經典運行-As 帳戶。 如果您仍然需要經典運行作為帳戶,請執行以下步驟。

1. 從 **「自動化帳戶」** 頁面中,選擇 **「 在帳戶設置**下**以帳戶身份運行**」。
2. 選擇**Azure 的經典執行為帳號**。
3. 按下"**創建**「以繼續創建經典」作為帳戶」。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關圖形創作的更多資訊,請參閱[Azure 自動化 中的圖形創作](automation-graphical-authoring-intro.md)。
* 要開始使用 PowerShell 執行簿,請參閱[我的第一個 PowerShell 執行簿](automation-first-runbook-textual-powershell.md)。
* 要開始使用 PowerShell 工作流執行簿,請參閱[我的第一個 PowerShell 工作流執行簿](automation-first-runbook-textual.md)。
* 要開始使用 Python2 執行簿,請參考[我的第一個 Python2 執行簿](automation-first-runbook-textual-python2.md)。

