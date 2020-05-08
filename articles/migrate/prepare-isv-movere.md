---
title: 準備 Azure Migrate 以使用 ISV tools/Movere
description: 準備 Azure Migrate 以使用 ISV tools/Movere
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906978"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>準備使用 ISV tools/Movere

如果您已將[ISV 工具](migrate-services-overview.md#isv-integration)或 Movere 新增至 Azure Migrate 專案，則在連結此工具之前，需要準備幾個步驟，並將資料傳送至 Azure Migrate。 

## <a name="check-azure-ad-permissions"></a>檢查 Azure AD 權限

您的 Azure 使用者帳戶需要幾個許可權：

- 向您的 Azure 租使用者註冊 Azure AD 應用程式的許可權。
- 在訂用帳戶層級將角色配置給 Azure AD 應用程式的許可權。


## <a name="set-permissions-to-register-an-azure-ad-app"></a>設定許可權以註冊 Azure AD 應用程式

1. 在 [Azure Active Directory 中，檢查您帳戶的角色。 如果您有使用者角色，請按一下左側的 [**使用者設定**]，並確認使用者是否可以註冊應用程式。
2. 如果設定為 **[是]**，則 Azure AD 租使用者中的任何使用者都可以註冊應用程式。 如果無法這樣做，則只有系統管理員使用者可以註冊應用程式。
3. 如果您沒有許可權，系統管理使用者可以提供具有[應用程式系統管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator)角色的使用者帳戶，讓您可以註冊應用程式。
4. 工具連結到 Azure Migrate 之後，系統管理員就可以從您的帳戶移除該角色。

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>設定許可權以將角色指派給 Azure AD 應用程式
 
在您的 Azure 訂用帳戶中，您的帳戶需要**Microsoft 授權/*/Write 存取權**，才能將角色指派給 Azure AD 應用程式。 

1. 若要檢查訂用帳戶許可權，請在 [Azure 入口網站中開啟 [**訂閱**]。
2. 選取相關的訂用帳戶。 如果您沒有看到它，請選取 [**全域訂閱] 篩選準則**。 
3. 選取 [我的權限]****。 然後，選取 [按一下這裡以詳細檢視此訂用帳戶的完整存取權]****。
4. 在 [**角色指派** > ]**視圖**中，檢查許可權。
5. 如果您的帳戶沒有許可權，請要求訂用帳戶管理員將您新增至「[使用者存取系統管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator)」角色或「[擁有](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)者」角色。
 

## <a name="start-using-the-tool"></a>開始使用工具

1. 如果您還沒有此工具的授權或免費試用版，請在 Azure Migrate 的工具專案中，按一下 [**註冊**] 中的 [**深入瞭解**]。
2. 在此工具中，遵循指示，從工具連結至 Azure Migrate 專案，並將資料傳送至 Azure Migrate。

## <a name="next-steps"></a>後續步驟

遵循 ISV 或 Movere 指示，將資料傳送至 Azure Migrate。

   
