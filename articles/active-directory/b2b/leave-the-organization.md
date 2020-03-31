---
title: 將組織保留為來賓使用者 - Azure 活動目錄
description: 說明 Azure AD B2B 來賓使用者如何使用 [存取面板] 從組織離職。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272502"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以來賓使用者的身分從組織離職

Azure Active Directory (Azure AD) B2B 使用者如果不再需要組織的應用程式，或不需要與組織保持關聯，隨時皆可決定從組織離職。 使用者可以自行從組織離職，不需要聯絡系統管理員。

> [!NOTE]
> 如果來賓使用者帳戶在家庭租戶或資源租戶中被禁用，則來賓使用者無法離開組織。 如果禁用其帳戶，來賓使用者將需要聯繫租戶管理員，租戶管理員可以刪除來賓帳戶或啟用來賓帳戶，以便使用者可以離開組織。

## <a name="leave-an-organization"></a>從組織離職

要離開組織，請按照以下步驟操作。

1. 通過執行以下步驟之一，轉到訪問面板設定檔頁面面：
   
   - 在[Azure 門戶](https://portal.azure.com)中，按一下右上角的名稱，然後選擇 **"查看帳戶**"。
   - 打開[訪問面板](https://myapps.microsoft.com)，按一下右上角的名稱，在 **"組織"** 旁邊，選擇設置圖示（齒輪）。
 
   ![顯示 [存取面板] 中使用者設定的螢幕擷取畫面](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > 如果您尚未登錄到要離開的組織，請在 **"組織"** 下按一下 **"登錄"，將組織連結保留**在組織名稱旁邊。 登錄後，在 **"組織"** 的右上角和旁邊再次按一下您的姓名，選擇設置圖示（齒輪）。

3. 在 [組織]**** 下方找到要離開的組織，然後選取 [從組織離職]****。

   ![顯示使用者介面中 [從組織離職] 選項的螢幕擷取畫面](media/leave-the-organization/LeaveOrg.png)

4. 系統要求您確認時，請選取 [離開]****。 

## <a name="account-removal"></a>帳戶移除

使用者從組織離職後，其帳戶將在目錄中「虛刪除」。 預設情況下，使用者物件移動到 Azure AD 中的 **"已刪除使用者**區域"，但在 30 天內不會永久刪除。 透過虛刪除的作法，若使用者在 30 天內提出要求，系統管理員便可還原使用者帳戶 (包括群組和權限)。

如有需要，租用戶系統管理員在 30 天內隨時可永久刪除帳戶。 作法：

1. 在 [[Azure 入口網站]](https://portal.azure.com) 中，選取 **[Azure Active Directory]**。
2. 在 [管理]**** 底下選取 [使用者]****。
3. 選取 [刪除的使用者]****。
4. 選取已刪除使用者旁邊的核取方塊，然後選取 [永久刪除]****。

若永久刪除使用者，此動作將無法回復。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD B2B 的概觀，請參閱[何謂 Azure AD B2B 共同作業？](what-is-b2b.md)



