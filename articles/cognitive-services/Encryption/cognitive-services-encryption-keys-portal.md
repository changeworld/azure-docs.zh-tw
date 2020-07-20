---
title: 認知服務的客戶管理金鑰
titleSuffix: Cognitive Services
description: 瞭解如何使用 Azure 入口網站，以 Azure Key Vault 設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1369f30c4a3bcb0a391a5f2b2a63191590afd622
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310694"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>使用適用于認知服務的 Azure Key Vault 來設定客戶管理的金鑰

使用認知服務的 Azure Key Vault 來啟用客戶管理金鑰的程式會因產品而異。 請使用下列連結來取得服務特定的指示：

## <a name="vision"></a>視覺

* [待用資料的加密自訂視覺](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [待用資料的臉部服務加密](../Face/face-encryption-of-data-at-rest.md)
* [待用資料的表單辨識器加密](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>語言

* [待用資料的 Language Understanding 服務加密](../LUIS/luis-encryption-of-data-at-rest.md)
* [待用資料的加密 QnA Maker](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [待用資料的 Translator 加密](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>決策

* [待用資料的內容仲裁加密](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [待用資料的個人化工具加密](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [認知服務客戶管理的金鑰要求表單](https://aka.ms/cogsvc-cmk)