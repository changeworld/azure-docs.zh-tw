---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186349"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選擇 **"應用程式**"，然後選擇 **"添加**"。
1. 輸入應用程式的名稱。 例如 *，testapp1*。
1. 針對 [Web 應用程式/Web API]****，選取 [是]****。
1. 對於**回復 URL**，輸入`https://jwt.ms`
1. 選取 [建立]****。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選取 [應用程式註冊 (預覽)]****，然後選取 [新增註冊]****。
1. 輸入應用程式的 [名稱]****。 例如 *，testapp1*。
1. 選擇**任何組織目錄中的帳戶或任何標識提供程式**。
1. 在**重定向 URI**下，選擇**Web** `https://jwt.ms` ，然後在 URL 文字方塊中輸入。
1. 在 [權限]**** 下，選取 [對 openid 與 offline_access 權限授與管理員同意]** 核取方塊。
1. 選取 [註冊]****。

完成應用程式註冊後，啟用隱式授予流：

1. 在 [管理]**** 底下，選取 [驗證]****。
1. 選取 [試用全新體驗]**** (若顯示的話)。
1. 在**隱式授予**下，選擇 **"訪問權杖**"和 **"ID 權杖"** 核取方塊。
1. 選取 [儲存]****。