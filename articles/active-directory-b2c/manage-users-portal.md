---
title: 創建&在 Azure 門戶中刪除 Azure AD B2C 消費者使用者帳戶
description: 瞭解如何使用 Azure 門戶在 Azure AD B2C 目錄中創建和刪除消費者使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187214"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>使用 Azure 門戶在 Azure AD B2C 中創建和刪除消費者使用者

在某些情況下，可能需要在 Azure 活動目錄 B2C （Azure AD B2C） 目錄中手動創建消費者帳戶。 儘管 Azure AD B2C 目錄中的消費者帳戶最常在使用者註冊使用其中一個應用程式時創建，但可以通過使用 Azure 門戶以程式設計方式創建它們。 本文重點介紹使用者創建和刪除的 Azure 門戶方法。

要添加或刪除使用者，必須為您的帳戶分配 *"使用者管理員*"或 *"全域管理員"* 角色。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>使用者帳戶的類型

如[Azure AD B2C 中的使用者帳戶概述中](user-overview.md)所述，可以在 Azure AD B2C 目錄中創建三種類型的使用者帳戶：

* Work
* 來賓
* 取用者

本文重點介紹在 Azure 門戶中使用**消費者帳戶**。 有關創建和刪除工作和來賓帳戶的資訊，請參閱[使用 Azure 活動目錄添加或刪除使用者](../active-directory/fundamentals/add-users-azure-active-directory.md)。

## <a name="create-a-consumer-user"></a>創建消費者使用者

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [管理]**** 底下選取 [使用者]****。
1. 選擇 **"新使用者**"。
1. 選擇**創建 Azure AD B2C 使用者**。
1. 選擇**登錄方法**，並為新使用者輸入**電子郵件地址**或**使用者名**。 此處選擇的登錄方法必須與為 Azure AD B2C 租戶的*本地帳戶*標識提供程式指定的設置匹配（請參閱在 Azure AD B2C 租戶中**管理** > **標識提供程式**）。
1. 輸入使用者**的名稱**。 這通常是使用者的全名（給定和姓氏）。
1. （可選）如果您希望延遲使用者登錄的能力，則可以**阻止登錄**。 稍後可以通過在 Azure 門戶中編輯使用者的**設定檔**來啟用登錄。
1. 選擇**自動生成密碼**或**讓我創建密碼**。
1. 指定使用者**的名字**和**姓氏**。
1. 選取 [建立]****。

除非您選擇了**塊登錄**，否則使用者現在可以使用您指定的登錄方法（電子郵件或使用者名）登錄。

## <a name="delete-a-consumer-user"></a>刪除消費者使用者

1. 在 Azure AD B2C 目錄中，選擇 **"使用者"，** 然後選擇要刪除的使用者。
1. 選擇 **"刪除**"，然後選擇 **"是**"以確認刪除。

有關在刪除後的前 30 天內還原使用者或永久刪除使用者的詳細資訊，請參閱[使用 Azure 活動目錄 還原或刪除最近刪除的使用者](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="next-steps"></a>後續步驟

有關自動使用者管理方案（例如將使用者從其他標識提供程式遷移到 Azure AD B2C 目錄），請參閱[Azure AD B2C：使用者遷移](user-migration.md)。
