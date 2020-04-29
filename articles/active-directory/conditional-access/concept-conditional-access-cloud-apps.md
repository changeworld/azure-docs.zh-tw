---
title: 條件式存取原則中的雲端應用程式或動作-Azure Active Directory
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
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457292"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>條件式存取：雲端應用程式或動作

雲端應用程式或動作是條件式存取原則中的關鍵信號。 條件式存取原則可讓系統管理員將控制項指派給特定的應用程式或動作。

- 系統管理員可以從包含內建 Microsoft 應用程式的應用程式清單，以及任何[Azure AD 整合式應用](../manage-apps/what-is-application-management.md)程式（包括資源庫、非資源庫和透過[應用程式 Proxy](../manage-apps/what-is-application-proxy.md)發佈的應用程式）中進行選擇。
- 系統管理員可以選擇根據雲端應用程式，但在使用者動作上定義原則。 唯一支援的動作是 [註冊安全性資訊（預覽）]，可讓條件式存取強制執行[結合安全性資訊註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md)的控制項。

![定義條件式存取原則並指定雲端應用程式](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 雲端應用程式

許多現有的 Microsoft 雲端應用程式都包含在您可以選取的應用程式清單中。 

系統管理員可以將條件式存取原則指派給 Microsoft 的下列雲端應用程式。 某些應用程式（例如 Office 365 （預覽）和 Microsoft Azure 管理）包含多個相關的子應用程式或服務。 下列清單並不完整，而且可能會變更。

- [Office 365 （預覽）](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 和資料倉儲](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights 分析
- [Microsoft Azure 資訊保護](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 管理](#microsoft-azure-management)
- Microsoft Azure 訂用帳戶管理
- Microsoft Cloud App Security
- Microsoft Commerce Tools 存取控制入口網站
- Microsoft Commerce 工具驗證服務
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 註冊](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Bing 中的 Microsoft 搜尋
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

### <a name="office-365-preview"></a>Office 365 （預覽）

Office 365 提供以雲端為基礎的生產力和共同作業服務，例如 Exchange、SharePoint 和 Microsoft 團隊。 Office 365 雲端服務已緊密整合，以確保順暢且共同作業的體驗。 這項整合可能會在建立原則時造成混淆，因為某些應用程式（例如 Microsoft 小組）與其他人（例如 SharePoint 或 Exchange）有相依性。

Office 365 （預覽）應用程式可讓您一次將這些服務設為目標。 我們建議使用新的 Office 365 （預覽）應用程式，而不是以個別雲端應用程式為目標。 以此應用程式群組為目標，有助於避免因原則和相依性不一致而可能發生的問題。

如果您想要加入 Office 365 （預覽）應用程式，並排除其在原則中選擇的特定應用程式，系統管理員可以選擇從原則中排除特定應用程式。

Office 365 （預覽）用戶端應用程式中所包含的主要應用程式：

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 搜尋服務
   - Office 365 Yammer
   - Office Delve
   - Office Online 
   - Office.com
   - OneDrive
   - PowerApps
   - 商務用 Skype Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 管理

Microsoft Azure 管理應用程式包含多個基礎服務。 

   - Azure 入口網站
   - Azure Resource Manager 提供者
   - 傳統部署模型 Api
   - Azure PowerShell
   - Visual Studio 訂閱系統管理員入口網站
   - Azure DevOps
   - Azure Data Factory 入口網站

> [!NOTE]
> Microsoft Azure 管理應用程式適用于 Azure PowerShell，其會呼叫 Azure Resource Manager API。 不適用於呼叫 Microsoft Graph 的Azure AD PowerShell。

## <a name="other-applications"></a>其他應用程式

除了 Microsoft 應用程式之外，系統管理員還可以將任何 Azure AD 註冊的應用程式新增至條件式存取原則。 這些應用程式可能包括： 

- 透過[Azure AD 應用程式 Proxy](../manage-apps/what-is-application-proxy.md)發佈的應用程式
- [從資源庫新增的應用程式](../manage-apps/add-application-portal.md)
- [不在資源庫中的自訂應用程式](../manage-apps/add-non-gallery-app.md)
- [透過應用程式傳遞控制器和網路發佈的繼承應用程式](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>使用者動作

使用者動作是可由使用者執行的工作。 目前唯一支援的動作是 [**註冊安全性資訊**]，這可讓條件式存取原則在已啟用合併註冊的使用者嘗試註冊其安全性資訊時強制執行。 如需詳細資訊，請參閱[結合安全性資訊註冊](../authentication/concept-registration-mfa-sspr-combined.md)一文。

## <a name="next-steps"></a>後續步驟

- [條件式存取：條件](concept-conditional-access-conditions.md)

- [條件式存取的一般原則](concept-conditional-access-policy-common.md)
- [用戶端應用程式相依性](service-dependencies.md)
