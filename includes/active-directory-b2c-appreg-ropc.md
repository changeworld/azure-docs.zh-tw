---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186981"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如， *ROPC_Auth_app*。
1. 對於 [原生用戶端]，選取 [是]。
1. 保留其他值，然後選取 [**建立**]。
1. 記錄 [應用程式識別碼]，以便在稍後的步驟中使用。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊 (預覽)]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如， *ROPC_Auth_app*。
1. 保留其他值，然後選取 [**註冊**]。
1. 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。
1. 在 [管理] 底下，選取 [驗證]。
1. 選取 [試用全新體驗] (若顯示的話)。
1. 在 [**預設用戶端類型**] 底下，選取 **[是]** ，將應用程式視為公用用戶端。 這是 ROPC 流程的必要設定。
1. 選取 [儲存]。