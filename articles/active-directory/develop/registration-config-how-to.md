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
ms.openlocfilehash: 2c348bd7a1738742744976c46396f62a3317dc17
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017071"
---
# <a name="how-to-discover-endpoints"></a>如何探索端點

您可以在 [Azure 入口網站](https://portal.azure.com)尋找應用程式的驗證端點。

1. 登入<a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 選取 **Azure Active Directory**。
1. 在 [ **管理**] 底下，選取 [ **應用程式註冊**]，然後選取頂端功能表中的 [ **端點** ]。

    [ **端點** ] 頁面隨即顯示，其中顯示您租使用者的驗證端點。
    
    使用與應用程式搭配使用的驗證通訊協定， **(用戶端) 識別碼** 來製作應用程式專屬的驗證要求。

**國家** 雲端 (例如 Azure AD 中國、德國和美國政府) 有自己的應用程式註冊入口網站和 Azure AD 驗證端點。 若要深入瞭解，請 [流覽國家](authentication-national-cloud.md)雲端。

## <a name="next-steps"></a>後續步驟

如需不同 Azure 環境中端點的詳細資訊，請參閱 [國家雲端總覽](authentication-national-cloud.md)。
