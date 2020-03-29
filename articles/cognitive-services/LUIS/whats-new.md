---
title: 新增功能 - 語言理解 （LUIS）
description: 本文定期更新有關 Azure 認知服務語言理解 API 的新聞。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 562f7f8b4974363daab91991e6a8219b352432fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155999"
---
# <a name="whats-new-in-language-understanding"></a>語言理解的新增功能

了解該服務的新功能。 這些專案包括版本資訊、視頻、博客文章和其他類型的資訊。 將此頁面加入書簽，以掌握服務的最新狀態。

## <a name="release-notes"></a>版本資訊

### <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 現在對對此服務的所有 HTTP 要求強制執行。 有關詳細資訊，請參閱[Azure 認知服務安全性](../cognitive-services-security.md)。

### <a name="november-4-2019---ignite"></a>2019年11月4日 - 點火

* 視頻 -[使用 LUIS 和 Azure 認知服務的高級自然語言理解 （NLU） 模型 |BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 提高開發人員工作效率
    * 預測[終結點 V3](luis-migration-api-v3.md)的一般可用性。
    * 能夠導入和匯出具有 .lu （[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)） 格式的應用程式. 這為有效的 CI/CD 進程鋪平了道路。
* 語言擴展
    * 在公共預覽中使用[阿拉伯文和印度文](luis-language-support.md)。
* 預先建置的模型
    * [預構建域](luis-reference-prebuilt-domains.md)現在通常可用 （GA）
    * V3 不支援日本[預構建實體](luis-reference-prebuilt-entities.md#japanese-entity-support)- 年齡、貨幣、數位和百分比。
    * 義大利[預構建實體](luis-reference-prebuilt-entities.md#italian-entity-support)- 年齡、貨幣、維度、數位和百分比解析度從 V2 更改為。
* [preview.luis.ai門戶](https://preview.luis.ai)中的增強使用者體驗 - 改進的標籤體驗，支援構建和調試複雜模型。 嘗試預覽門戶教程：
    * [僅意向](tutorial-intents-only.md)
    * [可分解機器學習實體](tutorial-machine-learned-entity.md)
* 提高語言理解能力 - 以更少的精力[構建複雜的語言模型](luis-concept-entity-types.md)。
* 在模型級別定義機器學習功能，並使模型能夠用作其他模型的信號，例如使用實體作為意圖和其他實體的要素。
* 新的擴展[限制](luis-boundaries.md)- 片語清單和總短語的最大值更高，新模型作為功能限制
* 以深層層次結構的形式從文本中提取資訊，使會話應用程式更強大。

    ![機器學習實體映射](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019年9月3日

* Azure 創作資源 -[立即遷移](luis-migration-authoring.md)。
    * 每個 Azure 資源有 500 個應用
    * 每個應用 100 個版本
* 土耳其對預建實體的支援
* 義大利對日期時間V2的支援

### <a name="july-23-2019"></a>七月 23， 2019

* 將[識別器文本](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3)更新為 1.2.3
    * 義大利文中的年齡、溫度、尺寸和貨幣識別器。
    * 提高英語假期識別能力，正確計算基於感恩節的日期。
    * 改進法語日期時間以減少非日期和非時間實體的誤報。
    * 支援日曆/學校/會計年度和英語日期範圍的首字母縮略詞。
    * 改進了中文和日語中的電話號碼識別。
    * 改進了對英語數位範圍的支援。
    * 效能改善。

### <a name="june-24-2019"></a>六月 24， 2019

* [OrdinalV2 預構建實體](luis-reference-prebuilt-ordinal-v2.md)，以支援排序，如下一個、上一個和最後一個。 只有英國文化。

### <a name="may-6-2019---build-conference"></a>2019 年 5 月 6 日 - //構建會議

在 2019 年構建大會上發佈了以下功能：

* [V3 API 遷移指南預覽](luis-migration-api-v3.md)
* [改進的分析儀表板](luis-how-to-use-dashboard.md)
* [改進的預構建域](luis-reference-prebuilt-domains.md)
* [動態清單實體](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [外部實體](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>部落格

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>影片

### <a name="2019-ignite-videos"></a>2019 點火器視頻

[使用 LUIS 和 Azure 認知服務的高級自然語言理解 （NLU） 模型 |BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 構建視頻

[如何使用 Azure 對話 AI 為下一代擴展業務](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服務更新

[認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
