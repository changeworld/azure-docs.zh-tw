---
title: 醫療保健的命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 00c1c8ddab9214bf7698c21b05c24afa36ec20d9
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147473"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>健康情況類別、實體和屬性的文字分析

[健全狀況文字分析](../../how-tos/text-analytics-for-health.md) 會偵測下列類別中的醫療概念。   (請注意，此容器預覽版僅支援英文文字，每個容器映射中只提供單一模型版本。 ) 


| 類別  | 描述  |
|---------|---------|
| [解剖](#anatomy) | 捕獲內文與 anatomic 系統、網站、地點或區域相關資訊的概念。 |
 | [人口](#demographics) | 可捕獲性別和年齡資訊的概念。 |
 | [考試](#examinations) | 捕捉診斷程式和測試相關資訊的概念。 |
 | [基因 組 學](#genomics) | 捕獲基因和變體相關資訊的概念。 |
 | [醫療](#healthcare) | 捕獲有關系統管理事件、護理環境和醫療保健職業相似性之資訊的概念。 |
 | [醫療條件](#medical-condition) | 捕捉診斷、徵兆或正負號相關資訊的概念。 |
 | [藥物](#medication) | 捕獲藥物相關資訊的概念，包括藥物名稱、類別、劑量和系統管理路由。 |
 | [社會](#social) | 此概念會捕捉有關 medically 相關社交層面的資訊，例如家族關聯性。 |
 | [治療](#treatment) | 捕獲 ekko-wave 治療程式相關資訊的概念。 |
  
每個類別可能包含兩個概念群組：

* **實體** -捕獲醫療概念的詞彙，例如診斷、藥物名稱或處理名稱。  例如， *bronchitis* 是一個診斷，而 *aspirin* 是一種藥物名稱。  此群組中的提及可以連結至 UMLS 概念識別碼。
* **屬性** -提供所偵測到實體之詳細資訊的片語，例如，「 *嚴重* 」是 *bronchitis* 或 *81 mg* 的條件辨識符號，是 *aspirin* 的劑量。  此類別中提及的將不會連結至 UMLS 概念識別碼。

此外，此服務會辨識不同概念之間的關聯性，包括屬性與實體之間的關聯性，例如，*主體結構* 的 *方向* *，或將* 實體 *名稱* 和實體之間的關聯性，例如縮寫偵測中的關係。

## <a name="anatomy"></a>結構

### <a name="entities"></a>實體

**BODY_STRUCTURE** 內文系統、anatomic 位置或區域，以及內文網站。 例如，arm、knee brace、abdomen、鼻子、liver、head、呼吸 system、lymphocytes。

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="主體結構實體的範例。":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="主體結構實體的展開範例。":::

### <a name="attributes"></a>屬性

**方向** 方向的詞彙（例如： left、橫向、upper、「事後 posterior），其特性為主體結構。

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="方向屬性的範例。":::

### <a name="supported-relations"></a>支援的關聯性

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>人口統計

### <a name="entities"></a>實體

**年齡** -所有年齡詞彙和片語，包括患者、家庭成員和其他人。 例如，40-歲、51 yo、3個月前、成人、嬰兒、年長、年輕、次要、中間過時。

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Age 實體的範例。":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Age 實體的另一個範例。":::


**性別** -公開主旨性別的條款。 例如，男性、女性、女性、跟、上班女郎。

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="性別實體的範例。":::

### <a name="attributes"></a>屬性

**RELATIONAL_OPERATOR** 片語，可表達人口統計實體與其他資訊之間的關聯性。

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="關聯式運算子的範例。":::

## <a name="examinations"></a>考試

### <a name="entities"></a>實體

**EXAMINATION_NAME** –診斷程式和測試。 例如，MRI、ECG、HIV test、hemoglobin、platelets count、scale 系統，例如 *Bristol stool scale*。

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="測驗實體的範例。":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="檢查名稱實體的另一個範例。":::

### <a name="attributes"></a>屬性

**方向** –表示檢查特性的方向性字詞。

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="具有檢查名稱實體的方向屬性範例。":::

**MEASUREMENT_UNIT** –檢查的單位。 例如，在 *hemoglobin > 9.5 g/dl* 中， *g/dl* 一詞是 *hemoglobin* 測試的單位。

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="具有檢查名稱實體的度量單位屬性範例。":::

**MEASUREMENT_VALUE** –檢查的值。 例如，在 *hemoglobin > 9.5 g/dL* 中，「 *9.5* 」一詞是 *hemoglobin* 測試的值。

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="具有檢查名稱實體的測量值屬性範例。":::

**RELATIONAL_OPERATOR** –表示檢查和其他資訊之間關聯性的片語。 例如，目標檢查所需的測量值。

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="具有檢查名稱實體的關聯式運算子範例。":::

**時間** -與檢查的開始和/或長度 (持續) 時間相關的時態性詞彙。 例如，測試發生時。

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="具有檢查名稱實體的時間屬性範例。":::

### <a name="supported-relations"></a>支援的關聯性

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>實體

**GENE** –所有提及的基因。 例如，MTRR、F2。

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Gene 實體的範例。":::

**變異** –所有提及的 gene 變化。 例如，c. 524C>T， (MTRR) ： r.1462_1557del96
  
## <a name="healthcare"></a>醫療保健

### <a name="entities"></a>實體
  
**ADMINISTRATIVE_EVENT** –與醫療保健系統相關但具有系統管理/半系統管理本質的事件。 例如，註冊、許可、試用、研究專案、傳輸、放電、住院治療、醫院保持不變。 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="醫療保健活動實體的範例。":::

**CARE_ENVIRONMENT** –患者會在意的環境或地點。 例如，緊急房間、醫師的辦公室、cardio 單位、hospice、醫院。

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="此螢幕擷取畫面顯示醫療保健環境實體的範例。":::

**HEALTHCARE_PROFESSION** –醫療保健實踐者授權或未經授權。 例如，牙醫、pathologist、neurologist、radiologist、藥劑師、nutritionist、實體 therapist、chiropractor。

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="此螢幕擷取畫面顯示醫療保健環境實體的另一個範例。":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="醫療保健環境實體的另一個範例。":::

## <a name="medical-condition"></a>醫療條件

### <a name="entities"></a>實體

**診斷** –疾病、症狀、中毒。 例如，breast 癌症、阿茲海默症、HTN、CHF、spinal 線的傷害。

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="醫療條件實體的範例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="醫療條件實體的另一個範例。":::

**SYMPTOM_OR_SIGN** –疾病或其他診斷的主觀或目標辨識項。 例如，胸難題、麻煩、dizziness、最近、專欄禁不住淚流滿面、abdomen 是軟性、良好的 bowel 音效、良好的 nourished。

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="醫療條件正負號或徵兆實體的範例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="醫療條件正負號或徵兆實體的另一個範例。":::

### <a name="attributes"></a>屬性

用來描述醫療條件的 **CONDITION_QUALIFIER** 品質詞彙。 下列子類別全都視為限定詞：

1.  時間相關的運算式：這些運算式是描述時間維度品質的詞彙，例如突然、銳角、經常性、長期以來非常。 
2.  品質運算式：這些是描述醫療條件「本質」的詞彙，例如燒錄、銳利。
3.  嚴重性運算式：嚴重、輕度、有點、無法控制。
4.  Extensivity 運算式： local、焦距、擴散。
5.  放射運算式： radiates、放射。
6.  條件比例：在某些情況下，條件會以尺規來表示，這是值的有限排序清單。 例如，具有階段 III pancreatic 癌症的患者。
7.  條件課程：與狀況的課程或進展相關的詞彙，例如改進、worsening、解決、remission。 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="條件辨識符號屬性和診斷實體的範例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="條件辨識符號屬性和診斷實體的另一個範例。":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="具有徵兆和藥物實體的條件辨識符號屬性範例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="此螢幕擷取畫面顯示另一個具有診斷實體之條件辨識符號屬性的範例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="此螢幕擷取畫面顯示具有診斷實體之條件辨識符號屬性的其他範例。":::

區分主體醫療條件的 **方向** 方向詞彙。

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="具有醫療條件實體的方向屬性範例。":::

**頻率** -發生或發生醫療狀況的頻率。

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="具有醫療條件實體的 frequency 屬性範例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="具有徵兆或正負號實體之 direction 屬性的另一個範例。":::

**MEASUREMENT_UNIT** -表示醫療狀況的單位。 例如，在 *1.5 x2x1 cm 腫瘤* 中， *cm* 是 *腫瘤* 的度量單位。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="使用醫療條件實體的度量單位屬性範例。":::

**MEASUREMENT_VALUE** -表示醫療條件的值。 例如，在 *1.5 x2x1 cm 腫瘤* 中， *1.5 x2x1* 是 *腫瘤* 的測量值。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="螢幕擷取畫面顯示具有徵兆或正負號實體的方向屬性範例。":::

**RELATIONAL_OPERATOR** 片語，表示醫療條件其他資訊之間的關聯性。 例如，時間或測量值。 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="螢幕擷取畫面顯示具有徵兆或正負號實體之 direction 屬性的另一個範例。":::

與醫療條件 (持續時間) 的開始和/或長度相關的 **時間** 時態詞彙。 例如，當徵兆開始 (驗證) 或疾病發生時。

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="螢幕擷取畫面顯示具有徵兆或正負號實體之 direction 屬性的其他範例。":::

### <a name="supported-relations"></a>支援的關聯性

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>藥物

### <a name="entities"></a>實體

**MEDICATION_CLASS** –一組具有類似動作機制的藥物、動作的相關模式、類似化學結構，以及/或用來處理相同疾病的一組藥物。 例如，ACE inhibitor、opioid、抗生素、難題 relievers。

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="藥物類別實體的範例。":::

**MEDICATION_NAME** –藥物提及，包括受著作權品牌的名稱和非品牌名稱。 例如，Advil、Ibuprofen。

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="藥物名稱實體的範例。":::

### <a name="attributes"></a>屬性

**劑量** -已訂購的藥物數量。 例如，注入 Sodium Chloride solution *1000 mL*。

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="藥物劑量屬性的範例。":::

**頻率** -應採取藥物的頻率。

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="藥物 frequency 屬性的範例。":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="藥物 frequency 屬性的另一個範例。":::

**MEDICATION_FORM** -藥物的形式。 例如，solution、框、膠囊、平板電腦、patch、凝膠、貼上、泡沫、噴灑、跌落、霜淇淋、syrup。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="藥物表單內容的範例。":::

**MEDICATION_ROUTE** -藥物的管理方法。 例如，口頭、vaginal、IV、epidural、主題、inhaled。

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="藥物路線屬性的範例。":::

**RELATIONAL_OPERATOR** 片語，可表達藥物與其他資訊之間的關聯性。 例如，所需的測量值。

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="螢幕擷取畫面顯示具有藥物實體的關聯式運算子屬性範例。":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="螢幕擷取畫面顯示具有藥物實體之關聯式運算子屬性的另一個範例。":::

### <a name="supported-relations"></a>支援的關聯性

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>社交

### <a name="entities"></a>實體

**FAMILY_RELATION** –提及主體的家人親屬。 例如，父親、女兒、同輩、父系。

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="螢幕擷取畫面顯示處理時間屬性的另一個範例。":::

## <a name="treatment"></a>處理方式

### <a name="entities"></a>實體

**TREATMENT_NAME** – ekko-wave 治療程式。 例如，knee brace 取代外科、骨骼 marrow transplant、TAVI、飲食。

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="處理名稱實體的範例。":::

### <a name="attributes"></a>屬性

面向處理方式的 **方向** 方向字詞。

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="螢幕擷取畫面顯示處理方向屬性的範例。":::

**頻率** -處理發生的頻率或應該發生的頻率。

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="螢幕擷取畫面顯示另一個處理方向屬性範例。":::
 
**RELATIONAL_OPERATOR** 片語，表示處理與其他資訊之間的關聯性。  例如，從上一個程式傳遞的時間量。

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="處理關聯式運算子屬性的範例。":::

與處理 (持續時間) 的開始和/或長度相關的 **時間** 時態詞彙。 例如，提供處理的日期。

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="螢幕擷取畫面顯示處理時間屬性的範例。":::

### <a name="supported-relations"></a>支援的關聯性

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
