---
title: 表單辨識器服務待用資料加密
titleSuffix: Azure Cognitive Services
description: 待用資料的表單辨識器加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: b897de94ad8cdb628520f9386b076f762a5cc230
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537947"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>待用資料的表單辨識器加密

Azure 表單辨識器會在將您的資料保存到雲端時，自動將其加密。 表單辨識器加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰只有在2020以後的11月後建立的可用資源。 若要搭配使用 CMK 與表單辨識器，您必須建立新的表單辨識器資源。 建立資源之後，您可以使用 Azure Key Vault 來設定受控識別。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>後續步驟

* [表單辨識器客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


