---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649102"
---
Azure Active Directory (Azure AD) 的受控識別可讓應用程式輕易存取其他受到 Azure AD 保護的資源，例如 Azure Key Vault。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 若要進一步了解 ADD 中的受控識別，請參閱 [Azure 資源的受控識別](../articles/active-directory/managed-identities-azure-resources/overview.md)。

您的應用程式可以授與兩種類型的身分識別：

- **系統指派的身分識別**會繫結至您的應用程式，如果您的應用程式已刪除，則會被刪除。 應用程式只能有一個系統指派的身分識別。
- **使用者指派的身分識別**是一項獨立 Azure 資源，可指派給您的應用程式。 應用程式可以有多個使用者指派的身分識別。