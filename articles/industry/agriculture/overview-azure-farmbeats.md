---
title: 什麼是 Azure FarmBeats
description: 提供 Azure FarmBeats 的概觀
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12bb0b0098b5108bf780b88fc42b86861ea6fcdc
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439566"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Azure FarmBeats 概觀 (預覽)

Azure FarmBeats 是可在 Azure Marketplace 中取得的企業對企業供應項目。 其可跨提供者來彙總農業資料集。 Azure FarmBeats 可讓您根據已融合的資料集來建置人工智慧 (AI) 或機器學習 (ML) 模型。 藉由使用 Azure FarmBeats，農業企業可以專注於核心的附加價值，而不是一視同仁地忙著進行資料工程。

> [!NOTE]
> Azure FarmBeats 目前處於公開預覽狀態。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 Azure Blockchain 目前沒有服務等級合約。 請使用 [Azure FarmBeats 支援論壇](https://aka.ms/farmbeatssupport) \(英文\) 來取得支援。

![專案 FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

透過 Azure FarmBeats 預覽版，您可以：

- 根據衛星影像，使用植被指數和水量指數來評估農場的健康情況。
- 取得要使用多少部土壤含水量感應器及要將其置於何處的建議。
- 藉由將來自各家廠商的感應器所收集的地面資料視覺化，來追蹤農場狀況。
- 根據衛星和感應器資料的融合來取得土壤水分圖。
- 在彙總的資料集之上建置 AI/ML 模型，來取得可行的見解。
- 提供農場健康情況諮詢，來建置或增強您的數位農業解決方案。

## <a name="datahub"></a>Datahub

Azure FarmBeats Datahub 是 API 層，可跨供應商對各種不同的農業資料集進行彙總、正規化及脈絡化。 您可以使用 Azure FarmBeats 來取得：
- **感應器資料**，來源是三個感應器提供者：[Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/)、[Teralytic](https://teralytic.com/)、[Pessl Instruments](https://metos.at/)
- **衛星影像**，來源是歐洲太空總署的 [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) 衛星任務
- **無人機影像**，來源是三個無人機影像提供者 [senseFly](https://www.sensefly.com/)、[SlantRange](https://slantrange.com/) 和 [DJI](https://dji.com/)

Datahub 會設計為可延伸的 API 平台。 我們正與更多供應商合作來整合 Azure FarmBeats，讓您在建置解決方案時有更多選擇。

## <a name="accelerator"></a>加速器

Azure FarmBeats 加速器是以 Datahub 為基礎所建置的範例 Web 應用程式。 此加速器會快速啟動使用者介面和模型的開發。 Azure FarmBeats 加速器會使用 Azure FarmBeats 的 API。 其會將內嵌感應器資料視覺化為圖表，並將模型輸出視覺化為地圖。 例如，您可以使用此加速器來快速建立農場，並輕鬆取得該農場的植被指數圖或感應器放置圖。

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

管理員可以針對使用其中一個預先定義角色的 Azure FarmBeats，為其定義存取規則。 角色會決定使用者可存取的應用程式區域，以及他們可以執行的動作。 Azure FarmBeats 中有兩種角色，分別適用於使用者和合作夥伴。

### <a name="user-roles"></a>使用者角色

[管理員可以新增及管理使用者](manage-users-in-azure-farmbeats.md)並根據兩個使用者角色定義其存取層級：管理員和唯讀。

### <a name="partner-roles"></a>夥伴角色

管理員可以將多個合作夥伴新增為 Azure FarmBeats 的資料提供者。 以下摘要說明 FarmBeats 中的可用合作夥伴角色及其權限：

| 合作夥伴類型    |   動作  | 影響範圍 |
| ---- | -------- | -------- |
| 感應器合作夥伴  |   建立、讀取、更新 <br/> <br/> 讀取、更新 | DeviceModel、Device、SensorModel Sensor <br/> <br/> ExtendedType |
| 映像合作夥伴  |   建立、讀取、更新 <br/> <br/> 讀取、更新 <br/> <br/> 讀取 | Scene、SceneFile <br/> <br/> ExtendedType <br/> <br/> Farm |
| 氣象合作夥伴* <br/> <br/>  (*即將推出) |   建立、讀取、更新 <br/> <br/> 讀取、更新 <br/> <br/> 讀取 | WeatherDataModel、WeatherDataLocation、JobType <br/> <br/> ExtendedType <br/> <br/> Farm |

## <a name="resources"></a>資源

Azure FarmBeats 會以免費的方式來提供，您只需就所使用的 Azure 資源支付費用。 您可以使用下列資源來深入了解此供應項目：

- 請造訪我們的 [Azure FarmBeats 部落格](https://aka.ms/farmbeatsblog)來隨時掌握最新的 Azure FarmBeats 新聞。
- 在我們的 [Azure FarmBeats 支援論壇](https://aka.ms/farmbeatssupport)上張貼問題即可尋求協助。
- 在我們的 [Azure FarmBeats 意見反應論壇](https://aka.ms/farmbeatsfeedback)上張貼功能的想法或對此投票，以提供意見反應。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [安裝 Azure FarmBeats](install-azure-farmbeats.md)
