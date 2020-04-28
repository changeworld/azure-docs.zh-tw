---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460543"
---
## <a name="protect-your-access-keys"></a>保護您的存取金鑰

您的儲存體帳戶存取金鑰類似于儲存體帳戶的根密碼。 請務必小心保護您的存取金鑰。 使用 Azure Key Vault 來安全地管理和旋轉金鑰。 避免將存取金鑰散發給其他使用者、進行硬式編碼，或將其儲存在其他人可以存取的純文字位置。 如果您認為金鑰可能遭到入侵，請將其旋轉。

可能的話，請使用 Azure Active Directory （Azure AD）來授權對 Blob 和佇列儲存體的要求，而不是共用金鑰。 Azure AD 透過共用金鑰提供更優異的安全性和易用性。 如需使用 Azure AD 授權存取資料的詳細資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](../articles/storage/common/storage-auth-aad.md)。
