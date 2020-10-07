---
title: QnA Maker 服務有什麼新功能？
titleSuffix: Azure Cognitive Services
description: 此文章包含有關 QnA Maker 的新聞。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 07/16/2020
ms.openlocfilehash: 73044f35b90fd3e5996899fd7c3b0a925056f8ed
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87836866"
---
# <a name="whats-new-in-qna-maker"></a>QnA Maker 的新功能

了解該服務的新功能。 這些項目可能是版本資訊、影片、部落格文章與其他類型的資訊。 將此頁面加入書簽，以掌握服務的最新狀態。

## <a name="release-notes"></a>版本資訊

了解 QnA Maker 的新功能。

### <a name="july-2020"></a>2020 年 7 月

* [中繼資料：多個中繼資料配對的 `OR` 邏輯組合](how-to/metadata-generateanswer-usage.md#logical-or-using-strictfilterscompoundoperationtype-property)
* 將認知搜尋端點設定為私人，但仍可供 QnA Maker 存取的[步驟](how-to/set-up-qnamaker-service-azure.md#configuring-cognitive-search-as-a-private-endpoint-inside-a-vnet)。
* 免費的認知搜尋資源會在[未使用達 90 天](how-to/set-up-qnamaker-service-azure.md#inactivity-policy-for-free-search-resources)後移除。

### <a name="june-2020"></a>2020 年 6 月

* 已更新 [Power Virtual Agent](tutorials/integrate-with-power-virtual-assistant-fallback-topic.md) 教學課程，使步驟更為流暢且簡易

### <a name="may-2020"></a>2020 年 5 月

* [Azure 角色型存取控制 (Azure RBAC)](concepts/role-based-access-control.md)
* [適用於回答的 RTF 編輯](how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="march-2020"></a>2020 年 3 月

* 現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

### <a name="february-2020"></a>2020 年 2 月

* 使用 GenerateAnswer API 的 [NPM 套件](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)

### <a name="november-2019"></a>2019 年 11 月

* QnA Maker 的[US Gov 雲端支援](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#qna-maker)
* GA 中的[多回合](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/multiturn-conversation)功能
* 第 1 層語言提供的[閒聊支援](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base#language-support)

### <a name="october-2019"></a>2019 年 10 月

* 針對 QnA Maker 服務中的所有知識庫[明確設定語言](./how-to/language-knowledge-base.md#select-language-when-creating-first-knowledge-base)。

### <a name="september-2019"></a>2019 年 9 月

* 使用 [XLS 檔案格式](concepts/content-types.md)匯入及匯出

### <a name="june-2019"></a>2019 年 6 月

* 已改善法文、義大利文、德文、西班牙文、葡萄牙文的[順位排定程式模型](concepts/query-knowledge-base.md#ranker-process)

### <a name="april-2019"></a>2019 年 4 月

* 支援網站內容擷取
* 來自已驗證存取的 [SharePoint 文件](how-to/add-sharepoint-datasources.md)支援

### <a name="march-2019"></a>2019 年 3 月

* [主動式學習](how-to/improve-knowledge-base.md)以真實使用者問題為基礎提供新問題替代項的建議
* 改善的英文版自然語言處理 (NLP) [順位排定程式](concepts/query-knowledge-base.md#ranker-process)模型

> [!div class="nextstepaction"]
> [建立 QnA Maker 服務](how-to/set-up-qnamaker-service-azure.md)

## <a name="cognitive-service-updates"></a>認知服務更新

[認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
