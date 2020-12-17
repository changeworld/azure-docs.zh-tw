---
title: IT 服務管理連接器總覽
description: 本文提供 IT 服務管理連接器 (ITSMC) 的總覽。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614552"
---
# <a name="it-service-management-connector-overview"></a>IT 服務管理連接器總覽

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT 服務管理連接器 (ITSMC) 可讓您將 Azure 連線到支援的 IT 服務管理 (ITSM) 產品或服務。

Azure 服務（例如 Azure Log Analytics 和 Azure 監視器）提供工具來偵測、分析和疑難排解 Azure 和非 Azure 資源的問題。 但是與問題相關的工作專案通常位於 ITSM 產品或服務中。 ITSMC 會提供 Azure 與 ITSM 工具之間的雙向連線，以協助您更快速地解決問題。

## <a name="configuration-steps"></a>組態步驟

ITSMC 支援與下列 ITSM 工具連線：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> 我們建議 Cherwell 和 Provance 客戶使用 [Webhook 動作](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) ，以 Cherwell 和 Provance 端點作為整合的另一種解決方案。

使用 ITSMC，您可以：

-  根據您的 Azure 警示 (計量警示、活動記錄警示及 Log Analytics 警示) ，在您的 ITSM 工具中建立工作專案。
-  您可以選擇將事件和變更要求資料從您的 ITSM 工具同步處理到 Azure Log Analytics 工作區。

如需法律條款和隱私權原則的詳細資訊，請參閱 [Microsoft 隱私權聲明](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)。

您可以藉由完成下列步驟來開始使用 ITSMC：

1. [將 ITSM 產品/服務與 IT 服務管理連接器連線。](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [新增 ITSMC。](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [建立 ITSM 連接。](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [使用連接。](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>後續步驟

[將 ITSM 產品/服務新增至 IT 服務管理連接器](./itsmc-connections.md)
