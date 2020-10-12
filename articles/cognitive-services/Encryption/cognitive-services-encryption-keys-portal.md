---
title: 認知服務的 Customer-Managed 金鑰
titleSuffix: Cognitive Services
description: 瞭解如何使用 Azure 入口網站，透過 Azure Key Vault 來設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 1369f30c4a3bcb0a391a5f2b2a63191590afd622
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84310694"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>使用認知服務的 Azure Key Vault 設定客戶管理的金鑰

使用認知服務的 Azure Key Vault 啟用 Customer-Managed 金鑰的程式會因產品而異。 使用這些連結來取得服務特定的指示：

## <a name="vision"></a>視覺

* [自訂視覺待用資料加密](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [靜態資料的臉部服務加密](../Face/face-encryption-of-data-at-rest.md)
* [靜止資料的表單辨識器加密](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>Language

* [靜態資料的 Language Understanding 服務加密](../LUIS/luis-encryption-of-data-at-rest.md)
* [QnA Maker 待用資料加密](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [待用資料的 Translator 加密](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>決策

* [靜態資料的內容仲裁加密](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [靜態資料的個人化工具加密](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>接下來的步驟

* [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [認知服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)