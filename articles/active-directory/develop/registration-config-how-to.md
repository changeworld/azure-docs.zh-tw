---
title: 取得 Azure AD 應用程式註冊的端點
titleSuffix: Microsoft identity platform
description: 如何尋找您正在開發或向 Azure AD 註冊之自訂應用程式的驗證端點。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82926675"
---
# <a name="how-to-discover-endpoints"></a>如何探索端點

您可以在 [Azure 入口網站](https://portal.azure.com)尋找應用程式的驗證端點。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **Azure Active Directory**。
1. 在 [**管理**] 底下，選取 [**應用程式註冊**]，然後選取頂端功能表中的 [**端點**]。

    [**端點**] 頁面隨即顯示，其中顯示您租使用者的驗證端點。
    
    使用符合您要搭配**應用程式（用戶端）識別碼**使用之驗證通訊協定的端點，來製作您的應用程式特定的驗證要求。

**國家**雲端（例如 Azure AD 中國、德國和美國政府）有自己的應用程式註冊入口網站和 Azure AD 驗證端點。 若要深入瞭解，請[流覽國家](authentication-national-cloud.md)雲端。

## <a name="next-steps"></a>後續步驟

如需有關不同 Azure 環境中端點的詳細資訊，請參閱[國家雲端總覽](authentication-national-cloud.md)。
