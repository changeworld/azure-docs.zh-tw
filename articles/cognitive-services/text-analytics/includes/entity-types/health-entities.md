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
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122553"
---
## <a name="health-entity-categories"></a>健康情況實體類別：

[文字分析的健全狀況](../../how-tos/text-analytics-for-health.md)會傳回下列實體類別。  請注意，此容器預覽僅支援英文文字，且每個容器映射中只會提供單一模型版本。

### <a name="named-entity-recognition"></a>具名實體辨識

|類別  |描述   |
|----------|--------------|
| 年齡 | 年齡. 例如*30 歲*。 |
| AdministrativeEvent | 系統管理事件。 |
| BodyStructure | 人類主體的部分，包括 organs 和其他結構。 例如*arm*或「*核心*」。 | 
| CareEnvironment | 管理護理或治療的環境。 例如*緊急室* | 
| ConditionQualifier | 條件層級。 例如，「*輕度*」、「*延伸*」或「*擴散*」。 | 
| 診斷 | 醫療條件。 例如*hypertension*。 | 
| 方向 | 說明. 例如*left*或*anterior*。 | 
| 劑量 | 藥物的大小或數量。 例如*25mg*。  | 
| ExaminationName | 檢查的方法或程式。 例如， *X 光線*。 | 
| RelationalOperator | 定義兩個實體之間關聯性的運算子。 例如，*小於*、或 `>=` 。  | 
| MeasurementUnit | 測量單位 (像百分比) 。 | 
| MeasurementValue | 度量單位的數值。 | 
| FamilyRelation | Familial 關聯性。 例如，[*備用*]。  | 
| 頻率 | 線.   | 
| 性別 | 性別. | 
| Gene | Gene 實體，例如*TP53*。   | 
| HealthcareProfession | 管理藥物的方法。 例如，*口頭 administration*。 | 
| MedicationClass | 藥物類別。 例如*抗生素*。  | 
| MedicationForm | 一種藥物形式。 例如，*膠囊*。 | 
| MedicationName  | 名為藥物的一般和品牌。 例如*ibuprofen*。 | 
| MedicationRoute | 管理藥物的方法。 例如，*口頭 administration*。 | 
| SymptomOrSign  | 疾病徵兆。 例如*讓人頭痛喉嚨*。 | 
| Time | 有時候. 例如*8 年*或*2：30AM 這個早上* |
| TreatmentName  | 處理名稱。 例如*治療*。 | 
| Variant | Gene 實體的遺傳變體。 | 

### <a name="relation-extraction"></a>關聯性解壓縮

關聯性解壓縮會識別文字中所述概念之間有意義的連接。 例如，藉由將條件名稱與時間產生關聯，即可找到「條件的時間」關聯性。 健全狀況的文字分析可以識別下列關聯性：

|類別  |描述   |
|----------|--------------|
| DirectionOfBodyStructure | 主體結構的方向。 |
| DirectionOfCondition | 條件的方向。 |
| DirectionOfExamination | 檢查的方向。 |
| DirectionOfTreatment | 處理的方向。 |
| TimeOfCondition | 與條件的萌芽相關聯的時間。 |
| QualifierOfCondition | 條件的相關聯限定詞。 |
| DosageOfMedication | 藥物的劑量。 |
| FormOfMedication | 一種藥物形式。 |
| RouteOfMedication | 使用藥品的路線或模式。 例如，*口頭*。 |
| FrequencyOfMedication | 使用藥物的頻率。 | 
| ValueOfCondition | 與條件相關聯的數值。 |
| UnitOfCondition | 單位 (，例如與條件相關聯的時間) 。 |
| TimeOfMedication | 使用藥物的時間。 |
| TimeOfTreatment | 管理處理的時間。 | 
| FrequencyOfTreatment | 管理處理的頻率。 |
| ValueOfExamination | 與檢查相關聯的數值。 | 
| UnitOfExamination | 單位 (，例如與檢查相關聯的百分比) 。 |
| RelationOfExamination | 實體與檢查之間的關聯性。 | 
| TimeOfExamination | 與檢查相關聯的時間。 |
| 縮寫 | 縮寫。  | 
