---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184334"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]****。
1. 在 **"管理**"下，選擇**應用註冊（舊版）**。
1. 選取 [新增應用程式註冊]****。
1. 輸入應用程式的名稱。 例如，*管理應用1。*
1. 對於**應用程式類型**，選擇**Web 應用程式 / API**。
1. 在登錄 URL 中輸入任何有效**URL。** 例如： `https://localhost` 。 終結點不需要訪問，但必須是有效的 URL。
1. 選取 [建立]****。
1. 記錄顯示在 **"已註冊應用**概覽"頁上**的應用程式 ID。** 在後面的步驟中使用此值。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
1. 選取 [應用程式註冊 (預覽)]****，然後選取 [新增註冊]****。
1. 輸入應用程式的 [名稱]****。 例如，*管理應用1。*
1. **僅選擇此組織目錄中的帳戶**。
1. 在 **"許可權**"下，清除 *"授予管理員同意打開 id 並offline_access許可權*"核取方塊。
1. 選取 [註冊]****。
1. 記錄應用程式 **（用戶端）ID**顯示在應用程式概述頁上。 在後面的步驟中使用此值。