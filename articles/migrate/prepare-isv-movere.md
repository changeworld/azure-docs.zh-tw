---
title: 準備 Azure Migrate 以便搭配 ISV 工具/Movere
description: 此文章描述如何準備 Azure Migrate 以搭配 ISV 工具或 Movere，然後描述如何開始使用此工具。
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: ed7652cb34705bac56a79b5c30e6bda3dac69af0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103920"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>準備使用 ISV 工具或 Movere

如果您已將 [ISV 工具](migrate-services-overview.md#isv-integration)或 Movere 新增至 Azure Migrate 專案，則在連結工具並將資料傳送至 Azure Migrate 之前，有幾個要遵循的步驟。 

## <a name="check-azure-ad-permissions"></a>檢查 Azure AD 權限

您的 Azure 使用者帳戶需要這些權限：

- 向您的 Azure 租用戶註冊 Azure Active Directory (Azure AD) 應用程式的權限
- 在訂用帳戶層級將角色配置給 Azure AD 應用程式的權限


### <a name="set-permissions-to-register-an-azure-ad-app"></a>設定權限以註冊 Azure AD 應用程式

1. 在 Azure AD 中，檢查您帳戶的角色。
2. 如果您有使用者角色，請選取左側的 [使用者設定]，並確認使用者是否可以註冊應用程式。 若設定為 [是]，則 Azure AD 租用戶中的任何使用者都可以註冊應用程式。 如果設定為 [否]，則只有系統管理員使用者可以註冊應用程式。   
3. 如果您沒有權限，則系統管理員使用者可以將[應用程式系統管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator)角色提供給您的使用者帳戶，讓您可以註冊應用程式。
4. 工具連結到 Azure Migrate 之後，系統管理員就可以從您的帳戶移除該角色。

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>設定權限以將角色指派給 Azure AD 應用程式
 
在您的 Azure 訂用帳戶中，您的帳戶需要 **Microsoft.Authorization/*/Write** 存取權，才能將角色指派給 Azure AD 應用程式。 

1. 在 Azure 入口網站中，開啟**訂用帳戶**。
2. 選取相關的訂用帳戶。 如果您沒有看到該訂用帳戶，請選取 [全域訂閱篩選]。 
3. 選取 [我的權限]。 然後，選取 [按一下這裡以詳細檢視此訂用帳戶的完整存取權]。
4. 在 [角色指派] > [檢視] 中，檢查權限。 如果您的帳戶沒有權限，請要求訂用帳戶管理員將您新增至[使用者存取系統管理員](../role-based-access-control/built-in-roles.md#user-access-administrator) \(部分機器翻譯\) 角色或[擁有者](../role-based-access-control/built-in-roles.md#owner) \(部分機器翻譯\) 角色。

## <a name="allow-access-to-urls"></a>允許存取 URL

針對 ISV 工具和 Azure 資料庫移轉小幫手，允許存取資料表中摘要說明的公用雲端 Url。 如果您使用 URL 型 Proxy 來連線到網際網路，請確定 Proxy 會解析查詢 URL 時所收到的任何 CNAME 記錄。 

**URL** | **詳細資料**
--- | ---
*.portal.azure.com  | 瀏覽至 Azure 入口網站。 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | 登入您的 Azure 訂用帳戶。 
*.microsoftonline.com<br/> *.microsoftonline-p.com | 建立 Azure Active Directory (AD) 應用程式，以便設備與 Azure Migrate 通訊。 
management.azure.com | 對 Azure Migrate 專案進行 Azure Resource Manager 呼叫。
*.servicebus.windows.net | 設備與 EventHub 之間用於傳送訊息的通訊。


## <a name="start-using-the-tool"></a>開始使用工具

1. 如果您還沒有此工具的授權或免費試用版，請在 Azure Migrate 的工具項目中，於 [註冊] 中，選取 [深入了解]。
2. 在工具中，遵循指示從工具連結至 Azure Migrate 專案，並將資料傳送至 Azure Migrate。

## <a name="next-steps"></a>後續步驟

遵循您 ISV 或 Movere 的指示，以將資料傳送至 Azure Migrate。

   
