---
title: 自訂視覺待用資料加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供 Microsoft 管理的加密金鑰，也可讓您使用自己的金鑰（稱為客戶管理的金鑰）來管理認知服務訂用帳戶， (CMK) 。 本文涵蓋自訂視覺的靜止資料加密，以及如何啟用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616207"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>自訂視覺待用資料加密

當您將資料保存到雲端時，Azure 自訂視覺會自動將您的資料加密。 自訂視覺加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰僅提供11月 11 2020 日之後建立的可用資源。 若要搭配自訂視覺使用 CMK，您將需要建立新的自訂視覺資源。 建立資源之後，您可以使用 Azure Key Vault 來設定受控識別。

## <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前可在下欄區域使用：

* 美國中南部
* 美國西部 2
* 美國東部
* US Gov 維吉尼亞州

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>後續步驟

* 如需支援 CMK 的服務完整清單，請參閱 [客戶管理的認知服務金鑰](../encryption/cognitive-services-encryption-keys-portal.md)
* [什麼是 Azure Key Vault](../../key-vault/general/overview.md)？
* [認知服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)