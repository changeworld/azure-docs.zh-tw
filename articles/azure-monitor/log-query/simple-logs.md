---
title: Azure 監視器 (Preview) 的簡單記錄體驗 |Microsoft Docs
description: 簡單的記錄檔體驗可讓您在 Azure 監視器中建立基本查詢，而不需要直接與 KQL 互動。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92929295"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure 監視器 (Preview 中的簡單記錄體驗) 
Azure 監視器可提供 [豐富的體驗](get-started-portal.md) ，讓您使用 KQL 語言來建立 [記錄查詢](log-query-overview.md) 。 但您可能不需要 KQL 的完整功能，而且偏好針對基本查詢需求簡化的體驗。 簡單的記錄檔體驗可讓您建立基本查詢，而不需要直接與 KQL 互動。 您也可以使用簡單記錄作為 KQL 的學習工具，因為您需要更複雜的查詢。

> [!NOTE]
> 簡單記錄目前實作為 Cosmos DB 和金鑰保存庫的測試。 請透過 [使用者心聲](https://feedback.azure.com/forums/913690-azure-monitor) 與 Microsoft 分享您的體驗，以協助我們判斷我們是否將擴充及發行這項功能。


## <a name="scope"></a>影響範圍
簡單的記錄體驗會從所選資源的 *AzureDiagnostics* 、 *AzureMetrics* 和 *AzureActivity* 資料表中取出資料。 

## <a name="using-simple-logs"></a>使用簡單記錄
流覽至您 Azure 訂用帳戶中的任何 Cosmos DB 或 Key Vault，並 [設定診斷設定來收集 Log Analytics 工作區中的記錄](../platform/resource-logs.md#send-to-azure-storage)。 按一下 [ **監視** ] 功能表中的 [ **記錄** 檔]，以開啟簡單的記錄體驗。

![螢幕擷取畫面顯示已選取記錄的 [監視] 功能表。](media/simple-logs/menu.png)

選取 **欄位** 和 **運算子** ，並指定比較的 **值** 。 按一下 **+** 並指定 **And/Or** 以新增其他準則。

![螢幕擷取畫面顯示已選取簡單記錄的 [記錄] 窗格中的搜尋。](media/simple-logs/criteria.png)

按一下 [ **執行** ] 以查看查詢結果。

## <a name="view-and-edit-kql"></a>查看和編輯 KQL
選取 [ **查詢編輯器** ]，以在完整的 Log Analytics 體驗中開啟簡單記錄查詢所產生的 KQL。 

![查詢編輯器](media/simple-logs/query-editor.png)

您可以直接編輯 KQL，並使用 Log Analytics 中的其他功能（例如篩選），以進一步精簡您的結果。

![編輯 KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>後續步驟

- 完成在 [Azure 入口網站中使用 Log Analytics](get-started-portal.md)的教學課程。
- 完成 [撰寫記錄查詢](get-started-portal.md)的教學課程。
