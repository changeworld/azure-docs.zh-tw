---
title: 條件式存取原則中的雲端應用程式或動作 - Azure Active Directory
description: 什麼是 Azure AD 條件式存取原則中的雲端應用程式或動作
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: defb959a56446da337c4c7c16ee118ceecd3e674
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846304"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>條件式存取：雲端應用程式或動作

雲端應用程式或動作是條件式存取原則中的關鍵訊號。 管理員可利用條件式存取原則，將控制項指派給特定的應用程式或動作。

- 管理員可以從應用程式清單中挑選，內有內建的 Microsoft 應用程式及任何 [Azure AD 整合式應用程式](../manage-apps/what-is-application-management.md) (包括資源庫、非資源庫以及透過[應用程式 Proxy](../manage-apps/what-is-application-proxy.md) 發佈的應用程式)。
- 管理員可以選擇根據使用者動作來定義原則，而非根據雲端應用程式。 唯一支援的動作是 [註冊安全性資訊]\(預覽)，可讓條件式存取強制執行[合併的安全性資訊註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md)相關的控制項。

![定義條件式存取原則並指定雲端應用程式](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 雲端應用程式

許多現有的 Microsoft 雲端應用程式都包含在您可從中選取的應用程式清單內。 

管理員可以將條件式存取原則指派給下列 Microsoft 的雲端應用程式。 某些如 Office 365 (預覽) 和 Microsoft Azure 管理等應用程式，則包含多個相關的子系應用程式或服務。 以下並非詳盡清單，且會隨時變動。

- [Office 365 (預覽)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 和資料倉儲](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights 分析
- [Microsoft Azure 資訊保護](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 管理](#microsoft-azure-management)
- Microsoft Azure 訂閱管理
- Microsoft Cloud App Security
- Microsoft Commerce Tools 存取控制入口網站
- Microsoft Commerce Tools 驗證服務
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 註冊](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Bing 專用 Microsoft 搜尋
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 服務
- Project Online
- 商務用 Skype Online
- 虛擬私人網路 (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (預覽)

Office 365 提供雲端型的生產力和共同作業服務，例如 Exchange、SharePoint 和 Microsoft Teams。 Office 365 雲端服務經過深度整合，可保證提供順暢的共同作業體驗。 這項整合會在建立原則時造成混淆，因為某些應用程式 (如 Microsoft Teams) 與其他應用程式 (如 SharePoint 或 Exchange) 有相依性。

Office 365 (預覽) 應用程式可讓您一次將這些服務全部設為目標。 建議使用新的 Office 365 (預覽) 應用程式，而非以個別的雲端應用程式為目標，以避免出現[服務相依性](service-dependencies.md)的問題。 以此應用程式群組做為目標，有助於避免發生因為原則和相依性不一致而導致的問題。

如果管理員想要從原則中排除特定應用程式，則可納入 Office 365 (預覽) 應用程式，並在原則中排除其選擇的特定應用程式。

Office 365 (預覽) 用戶端應用程式包含的主要應用程式：

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 Search Service
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - 商務用 Skype Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 管理

Microsoft Azure 管理應用程式包含多項基礎服務。 

   - Azure 入口網站
   - Azure Resource Manager 提供者
   - 傳統部署模型 API
   - Azure PowerShell
   - Visual Studio 訂閱管理員入口網站
   - Azure DevOps
   - Azure Data Factory 入口網站

> [!NOTE]
> Microsoft Azure 管理應用程式適用於可呼叫 Azure Resource Manager API 的 Azure PowerShell。 不適用於呼叫 Microsoft Graph 的 Azure AD PowerShell。

## <a name="other-applications"></a>其他應用程式

除了 Microsoft 應用程式之外，管理員還可以將任何 Azure AD 註冊應用程式新增至條件式存取原則。 這類應用程式可能包含： 

- 透過 [Azure AD 應用程式 Proxy](../manage-apps/what-is-application-proxy.md) 發佈的應用程式
- [從資源庫新增的應用程式](../manage-apps/add-application-portal.md)
- [不在資源庫中的自訂應用程式](../manage-apps/add-non-gallery-app.md)
- [透過應用程式傳遞控制器和網路發佈的舊版應用程式](../manage-apps/secure-hybrid-access.md)
- 使用以[密碼為基礎的單一登入](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)的應用程式

> [!NOTE]
> 由於條件式存取原則會訂定服務存取需求，因此無法將之套用到用戶端 (公用/原生) 應用程式。 換句話說，並不會直接在用戶端 (公用/原生) 應用程式上設定原則，而是在用戶端呼叫服務時套用。 例如，在 SharePoint 服務上設定的原則會套用至呼叫 SharePoint 的用戶端。 使用 Outlook 用戶端嘗試存取電子郵件時，會套用 Exchange 上設定的原則。 因此無法在雲端應用程式選擇器中選取用戶端 (公用/原生) 應用程式，且在您租用戶中註冊的用戶端 (公用/原生) 應用程式設定中，無法使用 [條件式存取] 選項。 


## <a name="user-actions"></a>使用者動作

使用者動作是可由使用者執行的工作。 目前唯一支援的動作是**登錄安全性資訊**，可在已啟用合併註冊的使用者嘗試註冊其安全性資訊時，讓條件式存取原則強制執行。 如需詳細資訊，請參閱[合併的安全性資訊註冊](../authentication/concept-registration-mfa-sspr-combined.md)一文。

## <a name="next-steps"></a>後續步驟

- [條件式存取：條件](concept-conditional-access-conditions.md)

- [條件式存取一般原則](concept-conditional-access-policy-common.md)
- [用戶端應用程式相依性](service-dependencies.md)
