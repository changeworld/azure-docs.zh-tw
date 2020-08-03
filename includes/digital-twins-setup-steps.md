---
author: baanders
description: 在 Azure 數位 Twins 設定中包含步驟總覽的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407427"
---
>[!NOTE]
>這些作業應由 Azure 訂用帳戶上具有*擁有*者角色的使用者完成。 雖然有些部分可以在沒有此提高許可權的情況下完成，但還是需要擁有者的合作，才能完整設定可用的實例。 如需詳細資訊，請參閱下列[*必要條件：必要許可權*](#prerequisites-permission-requirements)一節。

新的 Azure 數位 Twins 實例的完整設定包含三個部分：
1. **建立實例**
2. **設定使用者存取權限**： azure 使用者必須擁有 Azure 數位 Twins 實例上的*Azure 數位 Twins 擁有者（預覽）* 角色，才能管理它及其資料。 在此步驟中，您身為 Azure 訂用帳戶中的擁有者，會將此角色指派給將管理您的 Azure 數位 Twins 實例的人員。 這可能是您自己或組織中的其他人。
3. **設定用戶端應用程式的存取權限**：通常會撰寫將用來與您的實例互動的用戶端應用程式。 為了讓該用戶端應用程式存取您的 Azure 數位 Twins，您必須在用戶端應用程式將用來向實例進行驗證的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中設定*應用程式註冊*。

若要繼續，您將需要 Azure 訂用帳戶。 您可以在[這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免費設定一個。
