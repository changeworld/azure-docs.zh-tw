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
ms.openlocfilehash: 1d161c82c087fd86a3774f0d121330260b1574e4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366089"
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

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Key Vault](../../key-vault/general/overview.md)？
* [認知服務 Customer-Managed 金鑰要求表單](https://aka.ms/cogsvc-cmk)