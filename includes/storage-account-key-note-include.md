---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027321"
---
## <a name="protect-your-access-keys"></a>保護您的存取金鑰

您的儲存體帳戶存取金鑰類似于儲存體帳戶的根密碼。 請務必小心保護您的存取金鑰。 使用 Azure Key Vault 來安全地管理和旋轉金鑰。 避免將存取金鑰散發給其他使用者、進行硬式編碼，或將其儲存在其他人可以存取的純文字位置。 如果您認為金鑰可能遭到入侵，請將其旋轉。

> [!NOTE]
> Microsoft 建議您盡可能使用 Azure Active Directory （Azure AD）來授權對 blob 和佇列資料的要求，而不是共用金鑰。 Azure AD 透過共用金鑰提供更優異的安全性和易用性。 如需使用 Azure AD 授權存取資料的詳細資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](../articles/storage/common/storage-auth-aad.md)。
