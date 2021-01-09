---
title: 新增功能 - Language Understanding (LUIS)
description: 本文會定期更新 Azure 認知服務 Language Understanding API 的相關新聞。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 06/15/2020
ms.openlocfilehash: 52f8aad0a429d3378d46265fab719b1cb9f07fd0
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813185"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding 的新功能

了解該服務的新功能。 這些項目包含版本資訊、影片、部落格文章與其他類型的資訊。 將此頁面加入書籤，以掌握服務的最新狀態。

## <a name="release-notes"></a>版本資訊

### <a name="december-2020"></a>2020 年 12 月

* 所有 LUIS 使用者都必須[遷移至 LUIS authorint 資源](luis-migration-authoring.md)

### <a name="june-2020"></a>2020 年 6 月

* [預覽 3.0 編寫](luis-migration-authoring-entities.md) SDK -
    * 版本 3.2.0-preview.3 - [.NET - NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * 版本 4.0.0-preview.3 - [JS - NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* 使用 LUIS 套用 DevOps 實務
    * 概念
        * [適用於 LUIS 的 DevOps 實務](luis-concept-devops-sourcecontrol.md)
        * [適用於 LUIS DevOps 的持續整合與持續傳遞工作流程](luis-concept-devops-automation.md)
        * [測試 LUIS DevOps](luis-concept-devops-testing.md)
    * 使用方法
        * [使用 GitHub Actions 將 DevOps 套用至 LUIS 應用程式開發](luis-how-to-devops-with-github.md)
    * [完整的程式碼 GitHub 存放庫](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>2020 年 5 月 - //組建

* **正式推出** (GA)：
    * [Language Understanding 容器](luis-container-howto.md)
    * 預覽入口網站已升級為[目前入口網站](https://www.luis.ai)，[先前的](https://previous.luis.ai) 入口網站仍可使用
    * 新的機器學習實體建立和標記體驗
    * 從複合和簡單實體到機器學習實體的[升級程序](migrate-from-composite-entity.md)
    * 正規化字組變體的[設定](how-to-application-settings-portal.md)支援
* 預覽編寫 API 變更
    * 適用於巢狀機器學習實體的應用程式結構描述 7.x
    * [移轉至必要功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* 適用於開發人員的新資源
    * [持續整合工具](developer-reference-resource.md#continuous-integration-tools)
    * 工作坊 - 使用 LUIS 了解 [_自然語言理解_ (NLU) 的最佳做法](developer-reference-resource.md#workshops)
* [客戶管理的金鑰](luis-encryption-of-data-at-rest.md) - 使用您自己的金鑰來加密您在 LUIS 中使用的所有資料
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (影片) - 請參閱 LUIS 中的新功能



### <a name="march-2020"></a>2020 年 3 月

* 現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

### <a name="november-4-2019---ignite"></a>2019 年 11 月 4 日 - Ignite

* 影片 - [使用 LUIS 和 Azure 認知服務的進階自然語言理解 (NLU) 模型 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 已改善開發人員生產力
    * 正式推出我們的[預測端點 V3](luis-migration-api-v3.md)。
    * 能夠使用 `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) 格式來匯入和匯出應用程式。 這為有效的 CI/CD 程序做好準備。
* 語言擴充
    * 公開預覽的[阿拉伯文和印度文](luis-language-support.md)。
* 預先建置的模型
    * [預建網域](luis-reference-prebuilt-domains.md)現已正式推出 (GA)
    * 日文[預建實體](luis-reference-prebuilt-entities.md#japanese-entity-support) - V3 中不支援年齡、貨幣、數字和百分比。
    * 義大利文[預建實體](luis-reference-prebuilt-entities.md#italian-entity-support) - 已從 V2 變更年齡、貨幣、維度、數字和百分比解析。
* [preview.luis.ai 入口網站](https://preview.luis.ai)中的增強型使用者體驗 - 徹底翻新了標記體驗，以啟用建置和偵錯複雜模型。 試用預覽入口網站教學課程：
    * [僅限意圖](tutorial-intents-only.md)
    * [可分解的機器學習實體](tutorial-machine-learned-entity.md)
* 進階語言理解功能 - 輕鬆地[建置複雜的語言模型](luis-concept-entity-types.md)。
* 在模型層級定義機器學習功能，並讓模型可以用來做為其他模型的信號，例如使用實體做為意圖和其他實體的特徵。
* 新的已擴充[限制](luis-limits.md) - 更高的階段清單數上限及階段總數，做為功能限制的新模型
* 以深層階層結構的格式從文字中擷取資訊，讓對話應用程式更強大。

    ![機器學習實體映像](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019 年 9 月 3 日

* Azure 撰寫資源 - [立即遷移](luis-migration-authoring.md)。
    * 每個 Azure 資源 500 個應用程式
    * 每個應用程式 100 個版本
* 預建實體的土耳其文支援
* DatetimeV2 的義大利文支援

### <a name="july-23-2019"></a>2019 年 7 月 23 日

* 將 [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 更新至 1.2.3
    * 義大利文中的年齡、溫度、維度和貨幣辨識器。
    * 改善英文的假日辨識，以正確計算感恩節日期。
    * 改善法文 DateTime，以減少非日期和非時間實體的誤判。
    * 支援行事曆/學校/會計年度和英文 DateRange 中的縮略字。
    * 已改善中文和日文的 PhoneNumber 辨識。
    * 已改善英文的 NumberRange 支援。
    * 效能改善。

### <a name="june-24-2019"></a>2019 年 6 月 24 日

* [OrdinalV2 預建實體](luis-reference-prebuilt-ordinal-v2.md)，用來支援排序，例如下一個、上一個和最後一個。 僅限英文文化。

### <a name="may-6-2019---build-conference"></a>2019 年 5 月 6 日 - //組建會議

組建 2019 會議已發行下列功能：

* [V3 API 移轉指南的預覽](luis-migration-api-v3.md)
* [已改善分析儀表板](luis-how-to-use-dashboard.md)
* [已改善預建網域](luis-reference-prebuilt-domains.md)
* [動態清單實體](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [外部實體](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>部落格

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>影片

### <a name="2019-ignite-videos"></a>2019 Ignite 視訊

[使用 LUIS 和 Azure 認知服務的進階自然語言理解 (NLU) 模型 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 組建影片

[如何使用 Azure 對話 AI 來調整您的下一代商務](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服務更新

[認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
