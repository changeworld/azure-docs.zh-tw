---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定應用程式註冊的必要條件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303608"
---
若要驗證本文中使用的所有資源，您必須 **設定 [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) 應用程式註冊**。 請遵循 [操作說明：*建立應用程式註冊*](../articles/digital-twins/how-to-create-app-registration.md)以進行此設定。 

註冊應用程式之後，您將需要註冊的 **_應用程式 (用戶端) 識別碼_** 和 **_目錄 (租用戶) 識別碼_** ([在 Azure 入口網站中尋找](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id))。 請記下這些值，以便稍後用來授與 Azure Digital Twins API 的存取權。