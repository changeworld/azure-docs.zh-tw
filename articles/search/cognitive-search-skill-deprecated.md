---
title: 已淘汰的認知技能
titleSuffix: Azure Cognitive Search
description: 此頁面包含被視為已淘汰的認知技能清單，在不久的未來 Azure 認知搜尋技能集中不會提供支援。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72792024"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Azure 認知搜尋中已淘汰的認知技能

本文件描述已淘汰的認知技能。 使用下列指南取得內容：

* 技能名稱：即將淘汰的技能名稱，其對應至 @odata.type 屬性。
* 上次可用的 api 版本： Azure 認知搜尋公用 API 的最後一個版本，您可以在其中建立/更新包含對應已淘汰技能的技能集。
* 結束支援：對應技能已視為不受支援後的最後一天。 先前建立的技能集應會繼續運作，但建議使用者遷移出已淘汰的技能。
* 建議：將路徑遷移至受支援的技能。 建議使用者遵循建議，以便繼續獲得支援。

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>最後一個可用的 API 版本

2017-11-11-Preview

### <a name="end-of-support"></a>結束支援

2019 年 2 月 15 日

### <a name="recommendations"></a>建議 

請改用 [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)。 可提供 NamedEntityRecognitionSkill 的大多數功能，且品質更高。 在複雜輸出欄位中也具有更豐富的資訊。

若要遷移至[實體辨識技能](cognitive-search-skill-entity-recognition.md)，您必須對您的技能定義執行下列一或多項變更。 您可以使用 [Update Skillset API](https://docs.microsoft.com/rest/api/searchservice/update-skillset) (更新技能集 API) 來更新技能定義。

> [!NOTE]
> 目前不支援信賴分數作為概念。 `minimumPrecision` 參數會存在於 `EntityRecognitionSkill` 以供日後使用，並提供回溯相容性。

1. 「(必要)」** 將 `@odata.type` 從 `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` 變更至 `"#Microsoft.Skills.Text.EntityRecognitionSkill"`。

2. 「(選擇性)」** 如果您正在使用 `entities` 輸出，請改為使用 `EntityRecognitionSkill` 中的 `namedEntities` 複雜集合輸出。 您可以使用技能定義中的 `targetName`，將其對應至稱為 `entities` 的註釋。

3. 「(選擇性)」** 如果您未明確指定 `categories`，則除了先前受 `NamedEntityRecognitionSkill` 支援的類別外，`EntityRecognitionSkill` 可能會傳回不同類型的類別。 如果您不希望有此行為，請務必將 `categories` 參數明確設定為 `["Person", "Location", "Organization"]`。

    _範例移轉定義_

    * 簡單移轉

        _(之前) NamedEntityRecognition 技能定義_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(之後) EntityRecognition 技能定義_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * 較為複雜的移轉

        _(之前) NamedEntityRecognition 技能定義_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(之後) EntityRecognition 技能定義_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [實體辨識技能](cognitive-search-skill-entity-recognition.md)
