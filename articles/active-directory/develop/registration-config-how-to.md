---
title: 設定端點
description: 如何為針對 Azure AD 開發的應用程式或要向 Azure AD 註冊的自訂應用程式尋找驗證端點。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 043dc4f6e57620f58a1cf5f76db755703421800f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778731"
---
# <a name="how-to-configure-endpoints"></a>如何設定端點

您可以在 [Azure 入口網站](https://portal.azure.com)尋找應用程式的驗證端點。

-   流覽至 [ [Azure 入口網站](https://portal.azure.com)]。

-   從左方的瀏覽窗格，按一下 [Azure Active Directory]****。

-   按一下 [應用程式註冊]****，然後選擇 [端點]****。

-   這樣會開啟 [端點]**** 頁面，其中列出您租用戶的所有驗證端點。

-   使用您所使用之驗證通訊協定特定的端點搭配應用程式識別碼，來製作您應用程式的特定驗證要求。

**國家**雲端（例如 Azure AD 中國、德國和美國政府）有自己的應用程式註冊入口網站和 Azure AD 驗證端點。 若要深入瞭解，請[流覽國家](authentication-national-cloud.md)雲端。

## <a name="next-steps"></a>後續步驟
[Azure Active Directory 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
