---
title: 在 Azure 入口網站中建立 & 刪除 Azure AD B2C 取用者使用者帳戶
description: 瞭解如何使用 Azure 入口網站來建立和刪除 Azure AD B2C 目錄中的取用者使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d6d6169f8662c9b973fb7f624a590322f62b0b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387519"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>使用 Azure 入口網站來建立和刪除 Azure AD B2C 中的取用者使用者

在某些情況下，您可能會想要以手動方式在 Azure Active Directory B2C (Azure AD B2C) 目錄中建立取用者帳戶。 雖然當使用者註冊使用您的其中一個應用程式時，最常建立 Azure AD B2C 目錄中的取用者帳戶，但是您可以用程式設計的方式建立它們，並使用 Azure 入口網站。 本文著重于使用者建立和刪除的 Azure 入口網站方法。

若要新增或刪除使用者，您的帳戶必須獲指派「 *使用者管理員* 」或「 *全域管理員* 」角色。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>使用者帳戶的類型

如 Azure AD B2C 中 [的使用者帳戶總覽](user-overview.md)所述，您可以在 Azure AD B2C 目錄中建立三種類型的使用者帳戶：

* 工作
* 來賓
* 消費者

本文著重于使用 Azure 入口網站中的取用 **者帳戶** 。 如需有關建立和刪除工作和來賓帳戶的詳細資訊，請參閱 [使用 Azure Active Directory 新增或刪除使用者](../active-directory/fundamentals/add-users-azure-active-directory.md)。

## <a name="create-a-consumer-user"></a>建立取用者使用者

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [管理] 底下選取 [使用者]。
1. 選取 [新增使用者]。
1. 選取 [ **建立 Azure AD B2C 使用者**]。
1. 選擇 [登 **入] 方法** ，並輸入新使用者的 **電子郵件** 位址或使用者 **名稱** 。 您在此處選取的登入方法必須符合您為 Azure AD B2C 租使用者的*本機帳戶*識別提供者所指定的設定 (請參閱**Manage**  >  在 Azure AD B2C 租使用者) 中管理**識別提供者**。
1. 輸入使用者的 **名稱** 。 這通常是指定給使用者 (姓氏和姓氏) 的完整名稱。
1.  (選擇性) 如果您希望延遲使用者登入的能力，可以 **封鎖登入** 。 您稍後可以在 Azure 入口網站中編輯使用者的 **設定檔** ，以啟用登入。
1. 選擇 [ **自動產生密碼** ] 或 [ **讓我建立密碼**]。
1. 指定使用者的 **名字** 和 **姓氏**。
1. 選取 [建立]****。

除非您已選取 [ **封鎖登入**]，否則使用者現在可以使用您所指定的 [登入] 方法 (電子郵件或使用者名稱) 登入。

## <a name="delete-a-consumer-user"></a>刪除取用者使用者

1. 在您的 Azure AD B2C 目錄中，選取 [ **使用者**]，然後選取您要刪除的使用者。
1. 選取 [ **刪除** **]** ，然後確認刪除。

如需在刪除後的前30天內還原使用者的詳細資訊，或永久刪除使用者的詳細資訊，請參閱 [使用 Azure Active Directory 還原或移除最近刪除的使用者](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="next-steps"></a>後續步驟

針對自動化的使用者管理案例，例如將使用者從另一個身分識別提供者遷移至 Azure AD B2C 目錄，請參閱 [Azure AD B2C：使用者遷移](user-migration.md)。
