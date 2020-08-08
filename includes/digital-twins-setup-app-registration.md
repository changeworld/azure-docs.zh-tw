---
author: baanders
description: Azure 數位 Twins 設定中的 [存取權限] 步驟的 include 檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009633"
---
設定 Azure 數位 Twins 實例之後，通常會透過用戶端應用程式與該實例進行互動。 若要建立可運作的用戶端應用程式，您必須確定用戶端應用程式能夠針對 Azure 數位 Twins 進行驗證。 做法是設定[Azure Active Directory (Azure AD) ](../articles/active-directory/fundamentals/active-directory-whatis.md) **應用程式註冊**，以供用戶端應用程式使用。

此應用程式註冊可讓您設定[Azure 數位 Twins api](../articles/digital-twins/how-to-use-apis-sdks.md)的存取權限。 之後，用戶端應用程式會對應用程式註冊進行驗證，因此會授與對 Api 所設定的存取權限。

>[!TIP]
> 身為訂用帳戶擁有者/系統管理員，您可能會想要為每個新的 Azure 數位 Twins 實例設定新的應用程式註冊，*或*只執行這項工作一次，建立將在訂用帳戶中的所有 Azure 數位 Twins 實例之間共用的單一應用程式註冊。

### <a name="create-the-registration"></a>建立註冊