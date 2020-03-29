---
title: 使用 Azure 監視器集成 Splunk |微軟文檔
description: 瞭解如何使用 Azure 監視器將 Azure 活動目錄日誌與相撲邏輯集成
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968702"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>如何：使用 Azure 監視器將 Azure 活動目錄日誌與 Splunk 集成

在此文章中，您將了解如何使用 Azure 監視器將 Azure Active Directory (Azure AD) 記錄與 Splunk 整合。 您首先會將記錄路由傳送至 Azure 事件中樞，然後將事件中樞與 Splunk 整合。

## <a name="prerequisites"></a>Prerequisites

若要使用此功能，您必須要有：

- 包含 Azure AD 活動記錄的 Azure 事件中樞。 了解如何[將活動記錄串流至事件中樞](quickstart-azure-monitor-stream-logs-to-event-hub.md)。 

-  [微軟 Azure 為 Splunk 添加](https://splunkbase.splunk.com/app/3757/)。 

## <a name="integrate-azure-active-directory-logs"></a>集成 Azure 活動目錄日誌 

1. 開啟您的 Splunk 執行個體，並選取 [資料摘要]****。

    ![[資料摘要] 按鈕](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. 選取 [來源類型]**** 索引標籤，然後選取 [amal: aadal:audit]****

    ![[資料摘要] 的 [來源類型] 索引標籤](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    下圖顯示 Azure AD 活動記錄：

    ![活動記錄](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> 如果您無法在 Splunk 執行個體中安裝附加元件 (例如，如果您使用 Proxy，或在 Splunk Cloud 上執行)，您可以將這些事件轉送至 Splunk HTTP 事件收集器。 若要這樣做，請使用此 [Azure 函式](https://github.com/Microsoft/AzureFunctionforSplunkVS) \(英文\)，事件中樞中的新訊息會觸發此函式。 
>

## <a name="next-steps"></a>後續步驟

* [解釋 Azure 監視器中的稽核記錄架構](reference-azure-monitor-audit-log-schema.md)
* [在 Azure 監視器中解釋登錄日誌架構](reference-azure-monitor-sign-ins-log-schema.md)
* [常見問題集和已知問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)