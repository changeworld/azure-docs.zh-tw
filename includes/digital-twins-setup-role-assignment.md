---
author: baanders
description: 在 Azure 數位 Twins 設定中包含存取權限步驟的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: cbcaf4b4ad1b6c00f8c452582b986b6ee3b2806e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478816"
---
Azure 數位 Twins 會使用 [Azure Active Directory (Azure AD) ](../articles/active-directory/fundamentals/active-directory-whatis.md) 進行以角色為基礎的存取控制 (RBAC) 。 這表示在使用者可以對您的 Azure 數位 Twins 實例進行資料平面呼叫之前，該使用者必須獲指派具有適當許可權的角色。

針對 Azure 數位 Twins，此角色是 _**Azure 數位 Twins 資料擁有**_ 者。 您可以閱讀更多有關角色和安全性的 [*概念： Azure 數位 Twins 解決方案的安全性*](../articles/digital-twins/concepts-security.md)。

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]

本節將說明如何在您的 azure 訂用帳戶的 Azure AD 租使用者中，使用該使用者的電子郵件，為 Azure 數位 Twins 實例中的使用者建立角色指派。 視您組織中的角色而定，您可以自行設定此許可權，或代表將管理 Azure 數位 Twins 實例的其他人來設定它。

### <a name="assign-the-role"></a>指派角色

若要授與使用者管理 Azure 數位 Twins 實例的許可權，您必須將該實例內的 _**Azure 數位 Twins 資料擁有**_ 者角色指派給他們。

> [!NOTE]
> 請注意，此角色不同于 Azure AD *擁有* 者角色，也可以在 Azure 數位 Twins 實例的範圍內指派。 這些是兩個不同的管理角色，Azure AD *擁有* 者不會授與 *Azure 數位 Twins 資料擁有*者所授與的資料平面功能存取權。