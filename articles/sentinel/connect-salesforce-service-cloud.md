---
title: 將 Salesforce 服務雲端資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Salesforce 服務雲端資料連線器，將 Salesforce 記錄提取至 Azure Sentinel。 查看活頁簿中的 Salesforce 資料、建立警示及改進調查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 152fee2e4e72cee6a7bc1c768c0a8ca7b031ec39
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878913"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>將您的 Salesforce 服務雲端連接到 Azure Sentinel

> [!IMPORTANT]
> Salesforce 服務雲端連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

本文說明如何將您的 Salesforce 服務雲端解決方案連線至 Azure Sentinel。 Salesforce 服務雲端資料連線器可讓您輕鬆地將 Salesforce 資料與 Azure Sentinel 連線，讓您可以在活頁簿中加以查看、使用它來建立自訂警示，並加以併入以改善調查。 Salesforce 服務雲端與 Azure Sentinel 之間的整合會使用 REST API。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="prerequisites"></a>先決條件

- 您必須擁有 Azure Sentinel 工作區的 [讀取] 和 [寫入] 許可權。

- 您必須具有工作區共用金鑰的讀取權限。 [深入瞭解工作區金鑰](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

- 您必須具備 Azure Functions 的讀取和寫入權限，才能建立函數應用程式。 [深入瞭解 Azure Functions](../azure-functions/index.yml)。

- 您必須具有下列 Salesforce REST API 認證： **SALESFORCE Api 使用者名稱**、 **salesforce api 密碼**、 **salesforce 安全性權杖**、 **Salesforce 取用者金鑰**、 **salesforce 取用者秘密**。 [深入瞭解 Salesforce REST API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)。

## <a name="configure-and-connect-salesforce-service-cloud"></a>設定和連接 Salesforce 服務雲端

Salesforce 服務雲端可以將記錄直接整合和匯出至 Azure Sentinel。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **Salesforce Service Cloud (Preview])** 然後 **開啟 [連接器] 頁面**。

1. 遵循 [連接器] 頁面的 [設定 **] 區段中** 所述的步驟。

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料就會出現在 [ **記錄**] 的 [ **CustomLogs** ] 區段的 `SalesforceServiceCloud_CL` 資料表中。

請參閱連接器頁面中的 [ **下一個步驟** ] 索引標籤，以取得一些實用的範例查詢。

## <a name="validate-connectivity"></a>驗證連線能力

最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Salesforce 服務雲端連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。