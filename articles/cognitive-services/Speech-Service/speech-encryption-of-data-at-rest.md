---
title: 靜態資料的語音服務加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供 Microsoft 管理的加密金鑰，也可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋適用于語音服務的待用資料加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: b9b76b2eb5e9536561f73a92b6911a2f82122a1b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078090"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>靜態資料的語音服務加密

語音服務將您的資料保存到雲端時，會自動將您的資料加密。 語音服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 相容 [的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密進行加密和解密。 加密和解密是透明的，這表示會為您管理加密和存取。 根據預設，您的資料會受到保護，因此您無須修改程式碼或應用程式來利用加密功能。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

當您使用自訂語音和自訂語音時，語音服務可能會將下列資料儲存在雲端中：  

* 語音追蹤資料-只有在您開啟自訂端點的追蹤時
* 上傳定型和測試資料

根據預設，您的資料會儲存在 Microsoft 的儲存體中，且您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 您也可以選擇準備自己的儲存體帳戶。 存放區的存取權是由受控識別來管理，而語音服務無法直接存取您自己的資料，例如語音追蹤資料、自訂定型資料和自訂模型。

如需受控識別的詳細資訊，請參閱 [什麼是受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>自備儲存體 (BYOS) 進行自訂和記錄

若要要求存取以攜帶您自己的儲存體，請填寫並提交 [語音服務-自備儲存體 (BYOS) 要求表單](https://aka.ms/cogsvc-cmk)。 核准之後，您必須建立自己的儲存體帳戶，以儲存自訂和記錄所需的資料。 新增儲存體帳戶時，語音服務資源將會啟用系統指派的受控識別。 啟用系統指派的受控識別之後，會向 Azure Active Directory (AAD) 註冊此資源。 註冊之後，受控識別將獲得儲存體帳戶的存取權。 您可以在這裡深入瞭解受控識別。 如需受控識別的詳細資訊，請參閱 [什麼是受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除儲存體帳戶的存取權。 這會導致需要存取儲存體帳戶的語音服務部分停止運作。  

語音服務目前不支援客戶加密箱。 不過，您可以使用 BYOS 來儲存客戶資料，讓您可以達成類似的資料控制以 [客戶加密箱](../../security/fundamentals/customer-lockbox-overview.md)。 請記住，語音服務資料會保留，並在建立語音資源的區域中處理。 這適用于待用資料和傳輸中的資料。 使用自訂功能（例如自訂語音和自訂語音）時，所有客戶資料會在您的 BYOS (的相同區域中傳送、儲存和處理（如果使用) 和語音服務資源）。

> [!IMPORTANT]
> Microsoft 不 **會** 使用客戶資料來改善其語音模型。 此外，如果停用端點記錄且未使用任何自訂，則不會儲存任何客戶資料。 

## <a name="next-steps"></a>後續步驟

* [語音服務-自備儲存體 (BYOS) 要求形式](https://aka.ms/cogsvc-cmk)
* [什麼是受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。
