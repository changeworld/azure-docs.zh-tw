---
author: baanders
description: Azure 數位 Twins 設定中的 [存取權限] 步驟的 include 檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408260"
---
Azure 數位 Twins 會使用[Azure Active Directory （Azure AD）](../articles/active-directory/fundamentals/active-directory-whatis.md)來進行角色型存取控制（RBAC）。 這表示在使用者可以對您的 Azure 數位 Twins 實例進行資料平面呼叫之前，該使用者必須獲派具有適當許可權的角色。

針對 Azure 數位 Twins，此角色為_**Azure 數位 Twins 擁有者（預覽）**_。 如需角色和安全性的詳細資訊，請參閱[*概念： Azure 數位 Twins 解決方案的安全性*](../articles/digital-twins/concepts-security.md)。

本節將說明如何使用 Azure 訂用帳戶上的 Azure AD 租使用者中的使用者電子郵件，為您的 Azure 數位 Twins 實例中的使用者建立角色指派。 視貴組織中的角色而定，您可以自行設定此許可權，或代表將管理 Azure 數位 Twins 實例的其他人進行設定。

### <a name="assign-the-role"></a>指派角色

若要授與使用者管理 Azure 數位 Twins 實例的許可權，您必須將實例中的_**Azure 數位 Twins 擁有者（預覽）**_ 角色指派給他們。

> [!NOTE]
> 請注意，此角色不同于 Azure AD*擁有*者角色，也可以在 Azure 數位 Twins 實例的範圍中指派。 這些是兩個不同的管理角色，而 Azure AD*擁有*者並未授與*Azure 數位 Twins 擁有者（預覽）* 所授與之資料平面功能的存取權。