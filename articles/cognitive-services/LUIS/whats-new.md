---
title: 新功能-Language Understanding （LUIS）
description: 本文會定期更新 Azure 認知服務 Language Understanding API 的相關新聞。
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: 45bfe4e703942a7fa38329bd0905833e904e0ab1
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678744"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding 的新功能

了解該服務的新功能。 這些專案包括版本資訊、影片、blog 文章和其他類型的資訊。 將此頁面加入書籤，以掌握服務的最新狀態。

## <a name="release-notes"></a>版本資訊

### <a name="june-2020"></a>2020 年 6 月

* [預覽3.0 撰寫](luis-migration-authoring-entities.md)SDK
    * 版本 3.2.0-preview。 3- [.net-NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * 版本 4.0.0-preview。 3- [JS-NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)

### <a name="may-2020---build"></a>5月 2020-build

* 發行為正式**推出**（GA）：
    * [Language Understanding 容器](luis-container-howto.md)
    * 預覽入口網站已升級為[目前的入口](https://www.luis.ai)網站，[先前](https://previous.luis.ai)的入口網站仍可使用
    * 新的機器學習實體建立和標籤體驗
    * 從複合和簡單實體升級至機器學習實體的[流程](migrate-from-composite-entity.md)
    * [設定](how-to-application-settings-portal.md)正規化字變體的支援
* 預覽編寫 API 變更
    * 適用于嵌套機器學習服務實體的應用程式架構7。x
    * [遷移至必要功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* 開發人員的新資源
    * [持續整合工具](developer-reference-resource.md#continuous-integration-tools)
    * 研討會-[使用 LUIS 瞭解_天然 Language Understanding_ （NLU）的最佳作法](developer-reference-resource.md#workshops)
* [客戶管理的金鑰](luis-encryption-of-data-at-rest.md)-使用您自己的金鑰來加密您在 LUIS 中使用的所有資料
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) （影片）-請參閱 LUIS 中的新功能



### <a name="march-2020"></a>2020 年 3 月

* 現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

### <a name="november-4-2019---ignite"></a>2019年11月4日-Ignite

* 影片-[使用 LUIS 和 Azure 認知服務進行的天然自然 Language Understanding （NLU）模型 |BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 提升開發人員生產力
    * 正式推出的[預測端點 V3](luis-migration-api-v3.md)。
    * 能夠以 `.lu` （[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)）格式匯入和匯出應用程式。 這鋪路了有效 CI/CD 程式的方式。
* 語言擴充
    * [阿拉伯文和印度](luis-language-support.md)文（公開預覽）。
* 預先建置的模型
    * [預先](luis-reference-prebuilt-domains.md)建立的網域現已正式推出（GA）
    * 在 V3 中不支援日文預先建立的[實體](luis-reference-prebuilt-entities.md#japanese-entity-support)-年齡、貨幣、數位和百分比。
    * 義大利[建實體](luis-reference-prebuilt-entities.md#italian-entity-support)-從 V2 變更的年齡、貨幣、維度、數位和百分比解析。
* [Preview.luis.ai 入口網站](https://preview.luis.ai)中增強的使用者體驗-改頭換面標記體驗，以啟用建立和調試複雜模型。 試用預覽入口網站教學課程：
    * [僅意圖](tutorial-intents-only.md)
    * [分解機器學習實體](tutorial-machine-learned-entity.md)
* 預先語言理解功能-以較少的方式[建立複雜的語言模型](luis-concept-entity-types.md)。
* 在模型層級定義機器學習功能，並讓模型用來做為其他模型的信號，例如使用實體做為意圖和其他實體的功能。
* 新的擴充[限制](luis-limits.md)-片語清單的最大上限和片語總計、新模型做為功能限制
* 以深層階層結構的格式從文字中解壓縮資訊，讓對話應用程式更強大。

    ![機器學習服務實體影像](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019年9月3日

* Azure 撰寫資源-[立即遷移](luis-migration-authoring.md)。
    * 500每個 Azure 資源的應用程式
    * 100每個應用程式的版本
* 預建實體的土耳其文支援
* DatetimeV2 的義大利文支援

### <a name="july-23-2019"></a>2019年7月23日

* 將辨識器[文字](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3)更新為1.2。3
    * 義大利文中的年齡、溫度、維度和貨幣辨識器。
    * 改善英文版的假日辨識，以正確計算感恩節日期。
    * 改善法文 DateTime，以減少非日期和非時間實體的誤報。
    * 支援行事曆/學校/會計年度和英文 DateRange 的縮略字。
    * 已改善中文和日文的 PhoneNumber 辨識。
    * 已改善英文版 NumberRange 的支援。
    * 效能改善。

### <a name="june-24-2019"></a>2019年6月24日

* [OrdinalV2 預先](luis-reference-prebuilt-ordinal-v2.md)建立的實體以支援排序，例如下一個、上一個和最後一個。 僅限英文文化特性。

### <a name="may-6-2019---build-conference"></a>5月6日，2019-build 會議

組建2019會議已發行下列功能：

* [V3 API 遷移指南的預覽](luis-migration-api-v3.md)
* [改良的分析儀表板](luis-how-to-use-dashboard.md)
* [改良的預建網域](luis-reference-prebuilt-domains.md)
* [動態清單實體](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [外部實體](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>部落格

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>影片

### <a name="2019-ignite-videos"></a>2019 Ignite 影片

[使用 LUIS 和 Azure 認知服務的 Advanced 天然 Language Understanding （NLU）模型 |BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019組建影片

[如何使用 Azure 對話式 AI 來調整您的企業以進行下一代](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服務更新

[認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
