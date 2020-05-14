---
title: 待用資料的語音服務加密
titleSuffix: Azure Cognitive Services
description: 待用資料的語音服務加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: c2e52fbab8d984f7442d8a336e90e9f22c0bf061
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198661"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>待用資料的語音服務加密

當您的資料保存到雲端時，語音服務會自動將其加密。 語音服務加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-cognitive-services-encryption"></a>關於認知服務加密

資料會使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)相容[的256位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密來加密和解密。 加密和解密都是透明的，這表示會為您管理加密和存取。 您的資料預設是安全的，而且您不需要修改程式碼或應用程式，就能利用加密。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

當您使用自訂語音和自訂語音時，語音服務可能會將下列資料儲存在雲端中：  

* 語音追蹤資料-只有在您開啟自訂端點的追蹤時
* 已上傳定型和測試資料

根據預設，您的資料會儲存在 Microsoft 的儲存體中，而您的訂用帳戶會使用 Microsoft 管理的加密金鑰。 您也可以選擇準備您自己的儲存體帳戶。 存放區的存取權是由受控識別所管理，而語音服務無法直接存取您自己的資料，例如語音追蹤資料、自訂定型資料和自訂模型。

如需受控識別的詳細資訊，請參閱[什麼是受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>攜帶您自己的儲存體（BYOS）進行自訂和記錄

若要要求存取攜帶您自己的儲存體，請填寫並提交「 [語音服務-自備儲存體」（BYOS）要求表單](https://aka.ms/cogsvc-cmk)。 一經核准，您就必須建立自己的儲存體帳戶，以儲存自訂和記錄所需的資料。 新增儲存體帳戶時，語音服務資源會啟用系統指派的受控識別。 啟用系統指派的受控識別之後，此資源將會向 Azure Active Directory （AAD）註冊。 註冊之後，受控識別將會獲得儲存體帳戶的存取權。 您可以在這裡深入瞭解受控識別。 如需受控識別的詳細資訊，請參閱[什麼是受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對儲存體帳戶的存取。 這會導致需要存取儲存體帳戶的語音服務部分停止運作。  

語音服務目前不支援客戶加密箱。 不過，您可以使用 BYOS 來儲存客戶資料，讓您可以達成類似的資料控制項來[客戶加密箱](../../security/fundamentals/customer-lockbox-overview.md)。 請記住，語音服務資料會保留，並在語音資源建立所在的區域中進行處理。 這適用于待用資料和傳輸中的資料。 當您使用自訂功能（例如自訂語音和自訂語音）時，所有客戶資料都會在您的 BYOS （如果使用）和語音服務資源所在的相同區域中進行傳輸、儲存及處理。

> [!IMPORTANT]
> Microsoft 不**會**使用客戶資料來改善其語音模型。 此外，如果已停用端點記錄且未使用任何自訂專案，則不會儲存任何客戶資料。 

## <a name="next-steps"></a>後續步驟

* [語音服務-攜帶您自己的儲存體（BYOS）要求表單](https://aka.ms/cogsvc-cmk)
* [什麼是受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。
