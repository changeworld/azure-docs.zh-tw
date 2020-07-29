---
title: 醫療保健的命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 4ac65e85e05f408b8d2f37a1d6845dc9e28e2bab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373060"
---
## <a name="health-entity-categories"></a>健康情況實體類別：

[文字分析的健全狀況](../../how-tos/text-analytics-for-health.md)會傳回下列實體類別。  請注意，此容器預覽僅支援英文文字，且每個容器映射中只會提供單一模型版本。

### <a name="named-entity-recognition"></a>具名實體辨識

|類別  |描述   |
|----------|--------------|
| 存在 | 年齡. |
| BODY_STRUCTURE | 人類主體的部分，包括 organs 和其他結構。 | 
| CONDITION_QUALIFIER | 條件層級，例如 [*輕度*]、[*擴充*] 或 [*擴散*]。 | 
| 診斷 | 醫療條件。 例如*hypertension* 。 | 
| 方向 | 如*left*或*anterior*的指示。 | 
| 劑量 | 藥物的大小或數量。  | 
| EXAMINATION_NAME | 檢查的方法或程式。 | 
| EXAMINATION_RELATION | 測量單位與檢查之間的關聯。  | 
| EXAMINATION_UNIT | 用於檢查的測量單位。 | 
| EXAMINATION_VALUE | 檢查度量單位的值。 | 
| FAMILY_RELATION | Familial 關聯性，例如 [*備用*]。  | 
| 頻率 | 線.   | 
| 性別 | 性別. | 
| GENE | Gene 實體，例如*TP53*。   | 
| MEDICATION_CLASS | 藥物類別。 例如*抗生素*。  | 
| MEDICATION_NAME  | 名為藥物的一般和品牌。| 
| ROUTE_OR_MODE  | 管理藥物的方法。 | 
| SYMPTOM_OR_SIGN  | 疾病徵兆。 | 
| TIME  | 有時候. 例如「8年」或「2：30AM 這個早上」 |
| TREATMENT_NAME  | 處理名稱。 | 
| VARIANT  | Gene 實體的遺傳變體 | 

### <a name="relation-extraction"></a>關聯性解壓縮

關聯性解壓縮會識別文字中所述概念之間有意義的連接。 例如，藉由將條件名稱與時間產生關聯，即可找到「條件的時間」關聯性。 健全狀況的文字分析可以識別下列關聯性：

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* 縮寫 
