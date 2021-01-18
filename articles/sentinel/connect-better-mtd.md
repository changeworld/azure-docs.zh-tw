---
title: 將更好的行動裝置威脅防護 (MTD) 連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用更好的行動裝置威脅防護 (MTD) 資料連線器，將 MTD 記錄提取至 Azure Sentinel。 查看活頁簿中的 MTD 資料、建立警示及改進調查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541536"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>將更好的行動裝置威脅防護 (MTD) 連接至 Azure Sentinel

> [!IMPORTANT]
> 更好的行動裝置威脅防護 (MTD) 連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

更好的行動裝置威脅防禦 (MTD) 連接器，可讓您輕鬆地將所有更好的 MTD 安全性解決方案記錄與 Azure Sentinel 連接、查看儀表板、建立自訂警示，以及改進調查。 在更佳的行動裝置威脅防護和 Azure Sentinel 之間進行整合，將會使用 REST API。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-better-mobile-threat-defense"></a>設定並連接更好的行動裝置威脅防護

更好的 MTD 可以將記錄直接整合和匯出至 Azure Sentinel。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取更佳的行動裝置 **威脅防護 (MTD)  (Preview)** 然後 **開啟 [連接器] 頁面**。

1. 遵循 [連接器] 頁面上的步驟和 [此頁面上的 MTD 檔](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) ，以在更佳的 MTD 主控台上完成整合。

    當要求輸入 **工作區識別碼** 和 **主要金鑰** 值時，請從 Azure Sentinel 連接器頁面複製這些值，並將其貼到更好的 MTD 設定中。

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{工作區識別碼和主要金鑰}":::

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料會出現在 [ **記錄**] 的 [ **CustomLogs** ] 區段下的一或多個下列資料表中：
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

若要在分析規則、搜尋查詢或 Azure Sentinel 的其他地方查詢較佳的 MTD 記錄，請在查詢視窗頂端輸入上述資料表名稱之一。

## <a name="validate-connectivity"></a>驗證連線能力

最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將更佳的行動裝置威脅防護 (MTD) 連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何深入瞭解 [您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
