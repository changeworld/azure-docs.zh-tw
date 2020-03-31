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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460543"
---
## <a name="protect-your-access-keys"></a>保護您的訪問金鑰

存儲帳戶訪問金鑰類似于存儲帳戶的根密碼。 始終小心保護訪問金鑰。 使用 Azure 金鑰保存庫安全地管理和旋轉金鑰。 避免將訪問金鑰分發給其他使用者，對其進行硬編碼，或以其他人可訪問的純文字保存到任何位置。 如果您認為金鑰可能已洩露，請輪換這些金鑰。

如果可能，請使用 Azure 活動目錄 （Azure AD） 授權對 Blob 和佇列存儲的請求，而不是共用金鑰。 與共享金鑰一起，Azure AD 提供了卓越的安全性和易用性。 有關使用 Azure AD 授權訪問資料的詳細資訊，請參閱[使用 Azure 活動目錄 授權訪問 Azure blob 和佇列](../articles/storage/common/storage-auth-aad.md)。
