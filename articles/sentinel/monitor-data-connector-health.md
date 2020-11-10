---
title: 使用此 Azure Sentinel 活頁簿監視資料連線器的健康情況 |Microsoft Docs
description: 使用健康情況監視活頁簿來追蹤資料連線器的連線能力和效能。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 161e2d424611661619b99ecac3515aac6a8464e0
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428604"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>使用此 Azure Sentinel 活頁簿監視資料連線器的健康情況

**資料連線器健康情況監視活頁簿** 可讓您從 Azure Sentinel 內追蹤資料連線器的健康情況、連線能力和效能。 活頁簿會提供其他監視器、偵測異常狀況，並提供有關工作區資料內嵌狀態的見解。 您可以使用活頁簿的邏輯來監視內嵌資料的一般健全狀況，以及建立自訂視圖和以規則為基礎的警示。

## <a name="use-the-health-monitoring-workbook"></a>使用健康情況監視活頁簿

1. 從 Azure Sentinel 入口網站的 [ **威脅管理** ] 功能表中，選取 [活頁 **簿** ]。

1. 在 [活頁 **簿** ] 圖庫中，于 [搜尋] 列中輸入 *health* ，然後從結果中選取 [ **資料收集健康情況監視** ]。

1. 選取 [ **視圖範本** ] 以使用活頁簿，或選取 [ **儲存** ] 以建立活頁簿的可編輯複本。 建立複本時，請選取 [ **View saved saved 活頁簿** ]。

1. 一旦在活頁簿中，請先選取您想要查看的 **訂** 用帳戶和 **工作區** ，然後根據您的需求定義 **TimeRange** 來篩選資料。 您可以使用 [ **顯示說明** ] 切換來顯示活頁簿的就地說明。

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="資料連線器健康情況監視活頁簿登陸頁面" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

此活頁簿中有三個索引標籤式區段：

1. [ **總覽** ] 索引標籤會顯示所選工作區中資料內嵌的一般狀態： [量值]、[EPS 速率] 和 [上次接收記錄的時間]。

1. [ **資料收集異常** ] 索引標籤可協助您偵測資料收集程式中的異常（依資料表和資料來源）。 每個索引標籤都會顯示特定資料表的異常 ([ **一般** ] 索引標籤包含) 的資料表集合。 這些異常是使用傳回 **異常分數** 的 **Series_decompose_anomalies ( # B1** 函數來計算。 [深入瞭解此功能](https://docs.microsoft.com/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx)。 針對要評估的函式設定下列參數：

    - **AnomaliesTimeRange** ：這個時間選擇器只適用于資料收集異常的觀點。
    - **SampleInterval** ：在指定的時間範圍內取樣資料的時間間隔。 異常分數只會在最後一個間隔的資料上計算。
    - **PositiveAlertThreshold** ：這個值會定義正異常分數閾值。 它接受十進位值。
    - **NegativeAlertThreshold** ：這個值會定義負異常分數閾值。 它接受十進位值。

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="資料連線器健康情況監視活頁簿異常頁面" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. [ **代理程式資訊** ] 索引標籤會顯示您在各種電腦上所安裝 Log Analytics 代理程式（不論是 Azure VM、其他雲端 VM、內部部署 VM 或實體）的健全狀況相關資訊。 您可以監視下列各項：

   - 系統位置

   - 信號狀態和延遲

   - 可用的記憶體和磁碟空間

   - 代理程式作業

    在本節中，您必須選取描述電腦環境的索引標籤：如果您只想要查看受 Azure Arc 管理的機器，請選擇 [ **Azure 管理的電腦** ] 索引標籤。選擇 [ **所有電腦** ] 索引標籤，以查看已安裝 Log Analytics 代理程式的受控和非 Azure 電腦。

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="資料連線器健康情況監視活頁簿代理程式資訊頁面" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>後續步驟
瞭解如何將 [您的資料上架以 Azure Sentinel](quickstart-onboard.md)、 [連接資料來源](connect-data-sources.md)，以及瞭解 [您的資料和潛在威脅](quickstart-get-visibility.md)。
