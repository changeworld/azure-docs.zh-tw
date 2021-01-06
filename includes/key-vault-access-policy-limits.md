---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934524"
---
金鑰保存庫最多可支援 1024 個存取原則項目，每個項目分別會將一組不同的權限授與特定安全性主體。 由於這項限制，我們建議您盡可能將存取原則指派給使用者群組，而不是個別使用者。 使用群組可讓您更輕鬆地為組織中的多位人員管理許可權。 如需詳細資訊，請參閱 [使用 Azure Active Directory 群組管理應用程式和資源存取權](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

如需 Key Vault 存取控制的完整資訊，請參閱 [Azure Key Vault 安全性：身分識別和存取管理](../articles/key-vault/general/security-overview.md#identity-management)。