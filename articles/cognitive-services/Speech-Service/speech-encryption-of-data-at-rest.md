---
title: 靜態資料的語音服務加密
titleSuffix: Azure Cognitive Services
description: 靜態資料的語音服務加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372438"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>靜態資料的語音服務加密

語音服務在將資料保存到雲中時自動加密資料。 語音服務加密可保護您的資料，並説明您履行組織安全和合規性承諾。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密對資料進行加密和解密。 加密和解密是透明的，這意味著加密和訪問是為您管理的。 預設情況下，您的資料是安全的，您無需修改代碼或應用程式就可以利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

當您使用自訂語音和自訂語音時，語音服務可能會在雲中存儲以下資料：  

* 語音跟蹤資料 - 僅當打開自訂終結點的跟蹤時
* 上傳的培訓和測試資料

預設情況下，您的資料存儲在 Microsoft 的存儲中，您的訂閱使用 Microsoft 管理的加密金鑰。 您還可以選擇準備自己的存儲帳戶。 對存儲的訪問由託管標識管理，語音服務不能直接存取您自己的資料，如語音跟蹤資料、自訂訓練資料和自訂模型。

有關託管標識的詳細資訊，請參閱[什麼是託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>自帶存儲 （BYOS） 進行自訂和日誌記錄

要請求訪問以自帶存儲，請填寫並提交 [語音服務 - 自帶存儲 （BYOS） 請求表](https://aka.ms/cogsvc-cmk)。 獲得批准後，您需要創建自己的存儲帳戶來存儲自訂和日誌記錄所需的資料。 添加存儲帳戶時，語音服務資源將啟用系統分配的託管標識。 啟用系統分配的託管標識後，此資源將註冊到 Azure 活動目錄 （AAD）。 註冊後，託管標識將被授予對存儲帳戶的存取權限。 您可以在此處瞭解有關託管標識的更多資訊。 有關託管標識的詳細資訊，請參閱[什麼是託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系統分配的託管標識，將刪除對存儲帳戶的訪問。 這將導致需要訪問存儲帳戶的語音服務的某些部分停止工作。  

## <a name="regional-availability"></a>區域可用性

BYOS 目前在以下區域可用：

* 美國中南部
* 美國西部 2
* 美國東部

## <a name="next-steps"></a>後續步驟

* [語音服務 - 自帶存儲 （BYOS） 請求表](https://aka.ms/cogsvc-cmk)
* [什麼是託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
