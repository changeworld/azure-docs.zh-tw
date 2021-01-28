---
title: 受控識別和受信任的儲存體
description: 媒體服務可搭配受控識別使用，以啟用受信任的儲存體。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: 291508a6beaa687b3a10f55df4591ce601ab51a0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956169"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>使用媒體服務的受控識別和信任的儲存體

媒體服務可搭配 [受控](../../active-directory/managed-identities-azure-resources/overview.md) 識別使用，以啟用受信任的儲存體。 當您建立媒體服務帳戶時，您必須將它與儲存體帳戶產生關聯。 媒體服務可以使用系統驗證來存取該儲存體帳戶。 媒體服務會驗證媒體服務帳戶和儲存體帳戶是否位於相同的訂用帳戶中，而且會驗證新增關聯的使用者是否具有 Azure Resource Manager RBAC 的儲存體帳戶存取權。

## <a name="trusted-storage"></a>受信任的儲存體

但是，如果您想要使用防火牆來保護您的儲存體帳戶，您必須使用受控識別驗證。 它可讓媒體服務存取透過受信任的儲存體存取設定了防火牆或 VNet 限制的儲存體帳戶。  如需信任的 Microsoft 服務的詳細資訊，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

## <a name="media-services-managed-identity-scenarios"></a>媒體服務受控識別案例

目前有兩個案例可搭配媒體服務使用受控識別：

- 使用媒體服務帳戶的受控識別來存取儲存體帳戶。

- 使用媒體服務帳戶的受控識別來存取 Key Vault，以存取客戶金鑰。

接下來的兩節將說明這兩個案例中的差異。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>使用媒體服務帳戶的受控識別來存取儲存體帳戶

1. 使用受控識別建立媒體服務帳戶。
1. 將受控識別主體存取權授與您擁有的儲存體帳戶。
1. 媒體服務接著可以使用受控識別來代表您存取儲存體帳戶。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>使用媒體服務帳戶的受控識別來存取 Key Vault，以存取客戶金鑰

1. 使用受控識別建立媒體服務帳戶。
1. 將受控識別主體存取權授與您擁有的 Key Vault。
1. 設定媒體服務帳戶，以使用客戶金鑰型帳戶加密。
1. 媒體服務會使用受控識別來代表您存取 Key Vault。

如需有關客戶管理的金鑰和 Key Vault 的詳細資訊，請參閱 [使用媒體服務將您自己的金鑰 (客戶管理的金鑰) ](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>教學課程

這些教學課程包含上述兩個案例。

- [使用 Azure 入口網站來搭配媒體服務使用客戶自控金鑰或 BYOK](tutorial-byok-portal.md)
- [使用客戶管理的金鑰或 BYOK 搭配媒體服務 REST API](tutorial-byok-postman.md)。

## <a name="next-steps"></a>下一步

若要深入瞭解受控識別針對您和您的 Azure 應用程式所能做的動作，請參閱 [Azure AD 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。