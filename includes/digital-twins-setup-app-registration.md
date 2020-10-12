---
author: baanders
description: 在 Azure 數位 Twins 設定中包含存取權限步驟的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009633"
---
設定 Azure 數位 Twins 實例之後，通常會透過用戶端應用程式與該實例進行互動。 若要建立可運作的用戶端應用程式，您必須確定用戶端應用程式能夠針對 Azure 數位 Twins 進行驗證。 這是藉由設定 [Azure Active Directory (Azure AD ](../articles/active-directory/fundamentals/active-directory-whatis.md)用戶端應用程式要使用的) **應用程式註冊** 來完成。

此應用程式註冊可讓您設定 [Azure 數位 Twins api](../articles/digital-twins/how-to-use-apis-sdks.md)的存取權限。 稍後，用戶端應用程式會向應用程式註冊進行驗證，因此會將設定的存取權限授與 Api。

>[!TIP]
> 訂用帳戶擁有者/系統管理員，您可能會想要為每個新的 Azure 數位 Twins 實例設定新的應用程式註冊， *或* 只進行一次，建立單一應用程式註冊，以在訂用帳戶中的所有 Azure 數位 Twins 實例之間共用。

### <a name="create-the-registration"></a>建立註冊