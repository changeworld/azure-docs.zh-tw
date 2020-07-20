---
title: 待用資料的加密自訂視覺
titleSuffix: Azure Cognitive Services
description: 待用資料的加密自訂視覺。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310286"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>待用資料的加密自訂視覺

Azure 自訂視覺會在將資料保存到雲端時，自動將其加密。 自訂視覺加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰只有在2020以後的11月後建立的可用資源。 若要搭配自訂視覺使用 CMK，您將需要建立新的自訂視覺資源。 建立資源之後，您可以使用 Azure Key Vault 來設定受控識別。

## <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前適用于下欄區域：

* 美國中南部
* 美國西部 2
* 美國東部
* US Gov 維吉尼亞州

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>後續步驟

* 如需支援 CMK 之服務的完整清單，請參閱[客戶管理的金鑰，以取得認知服務](../encryption/cognitive-services-encryption-keys-portal.md)
* [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [認知服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
