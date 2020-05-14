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
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202556"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>待用資料的加密自訂視覺

Azure 自訂視覺會在將資料保存到雲端時，自動將其加密。 自訂視覺加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客戶管理的金鑰只有在2020以後的11月後建立的可用資源。 若要搭配自訂視覺使用 CMK，您將需要建立新的自訂視覺資源。 建立資源之後，您可以使用 Azure Key Vault 來設定受控識別。

### <a name="regional-availability"></a>區域可用性

客戶管理的金鑰目前適用于下欄區域：

* 美國中南部
* 美國西部 2
* 美國東部
* US Gov 維吉尼亞州

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>後續步驟

* [自訂視覺客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)
* [深入瞭解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


