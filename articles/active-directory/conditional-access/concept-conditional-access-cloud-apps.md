---
title: 條件訪問策略中的雲應用或操作 - Azure 活動目錄
description: Azure AD 條件訪問策略中的雲應用或操作
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
ms.openlocfilehash: 69bdd2d6825427597e9030a03aae7d219361ba25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671938"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>條件訪問：雲應用或操作

雲應用或操作是條件訪問策略中的關鍵信號。 條件訪問策略允許管理員將控制項分配給特定應用程式或操作。

- 管理員可以從包含內置 Microsoft 應用程式的應用程式清單和任何[Azure AD 集成應用程式](../manage-apps/what-is-application-management.md)（包括庫、非庫和通過[應用程式代理](../manage-apps/what-is-application-proxy.md)發佈的應用程式）中進行選擇。
- 管理員可以選擇定義策略，而不是基於雲應用程式，而是基於使用者操作。 唯一支援的操作是註冊安全資訊（預覽），允許條件訪問強制實施圍繞[組合安全資訊註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md)的控制。

![定義條件訪問策略並指定雲應用](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 雲端應用程式

許多現有的 Microsoft 雲應用程式都包含在可以選擇的應用程式清單中。 

管理員可以將條件訪問策略分配給 Microsoft 的以下雲應用。 某些應用（如 Office 365（預覽版）和 Microsoft Azure 管理包括多個相關的子應用或服務。 以下清單並非詳盡無遺，可能會更改。

- [辦公室 365 （預覽版）](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 和資料倉儲](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- 微軟應用程式洞察分析
- [Microsoft Azure 資訊保護](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 管理](#microsoft-azure-management)
- 微軟 Azure 訂閱管理
- Microsoft Cloud App Security
- 微軟商務工具存取控制門戶
- 微軟商務工具認證服務
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 註冊](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- 微軟搜索在必應
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- 辦公室斯威
- Outlook Groups
- Power BI 服務
- Project Online
- 商務用 Skype Online
- 虛擬私人網路 (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>辦公室 365 （預覽版）

Office 365 提供基於雲的生產力和協作服務，如 Exchange、SharePoint 和 Microsoft 團隊。 Office 365 雲服務深度集成，可確保流暢和協作的體驗。 在創建策略（如 Microsoft Teams）等應用依賴于其他應用（如 SharePoint 或 Exchange）時，此集成可能會導致混淆。

Office 365（預覽版）應用可以同時定位這些服務。 我們建議使用新的 Office 365（預覽）應用，而不是針對單個雲應用。 定位此應用程式組有助於避免由於策略和依賴項不一致而可能出現的問題。

如果管理員願意，可以選擇從策略中排除特定應用，包括 Office 365（預覽版）應用，並在策略中排除自己選擇的特定應用。

Office 365（預覽）用戶端應用中包含的關鍵應用程式：

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - 辦公室 365 搜索服務
   - Office 365 Yammer
   - Office Delve
   - Office Online 
   - Office.com
   - OneDrive
   - PowerApps
   - 商務用 Skype Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 管理

Microsoft Azure 管理應用程式包括多個基礎服務。 

   - Azure 入口網站
   - Azure 資源管理器提供程式
   - 經典部署模型 API
   - Azure PowerShell
   - 視覺化工作室訂閱管理員門戶
   - Azure DevOps
   - Azure 資料工廠門戶

> [!NOTE]
> Microsoft Azure 管理應用程式應用於 Azure PowerShell，後者調用 Azure 資源管理器 API。 不適用於呼叫 Microsoft Graph 的Azure AD PowerShell。

## <a name="other-applications"></a>其他應用程式

除了 Microsoft 應用之外，管理員還可以將任何 Azure AD 註冊的應用程式添加到條件訪問策略。 這些應用程式可能包括： 

- 通過[Azure AD 應用程式代理髮布的應用程式](../manage-apps/what-is-application-proxy.md)
- [從庫中添加的應用程式](../manage-apps/add-application-portal.md)
- [不在庫中的自訂應用程式](../manage-apps/add-non-gallery-app.md)
- [通過應用交付控制器和網路發佈的舊應用程式](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>使用者動作

使用者操作是使用者可以執行的任務。 當前唯一支援的操作是**註冊安全資訊（預覽），** 它允許條件訪問策略在啟用合併註冊的使用者嘗試註冊其安全資訊時強制執行。 更多資訊請參閱文章，[組合安全資訊註冊（預覽）。](../authentication/concept-registration-mfa-sspr-combined.md)

## <a name="next-steps"></a>後續步驟

- [條件訪問：條件](concept-conditional-access-conditions.md)

- [條件訪問通用策略](concept-conditional-access-policy-common.md)
- [用戶端應用程式依賴項](service-dependencies.md)
