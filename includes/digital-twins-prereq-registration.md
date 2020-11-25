---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定應用程式註冊的必要條件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020293"
---
若要驗證本文中使用的所有資源，您必須設定 [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) **應用程式註冊**。 請遵循 [操作說明：*建立應用程式註冊*](../articles/digital-twins/how-to-create-app-registration.md)以進行此設定。 

註冊應用程式之後，您將需要註冊的 **_應用程式 (用戶端) 識別碼_** 和 **_目錄 (租用戶) 識別碼_** ([在 Azure 入口網站中尋找](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id))。 請記下這些值，以便稍後用來授與 Azure Digital Twins API 的存取權。