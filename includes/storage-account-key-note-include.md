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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027321"
---
## <a name="protect-your-access-keys"></a>保護您的存取金鑰

您的儲存體帳戶存取金鑰類似于儲存體帳戶的根密碼。 請務必小心保護您的存取金鑰。 使用 Azure Key Vault 來安全地管理及輪替您的金鑰。 避免將存取金鑰散發給其他使用者、進行硬式編碼，或將其儲存在任何地方以可供他人存取的純文字。 如果您認為金鑰可能已遭盜用，請輪替金鑰。

> [!NOTE]
> Microsoft 建議您盡可能使用 Azure Active Directory (Azure AD) ，將要求授與 blob 和佇列資料，而不是共用金鑰。 Azure AD 可提供更高的安全性，並讓您輕鬆地透過共用金鑰使用。 如需有關使用 Azure AD 授權存取資料的詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 Azure blob 和佇列](../articles/storage/common/storage-auth-aad.md)。
