---
title: 設定 Azure 監視器中的代理程式資料來源 | Microsoft Docs
description: 資料來源定義 Azure 監視器會從代理程式和其他已連接的來源收集哪些資料。  本文說明 Azure 監視器如何使用資料來源的概念、詳細說明如何設定資料來源，並提供可用的不同資料來源的摘要。.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249096"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Azure 監視器中的代理程式資料來源
Azure 監視器從代理程式收集的資料由您設定的資料來源設定。  系統會將來自代理程式的資料連同一組記錄儲存成[記錄資料](data-platform-logs.md)。  每個資料來源都會建立特定類型的記錄，每種類型各有自己的一組屬性。

![記錄資料收集](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>資料來源的摘要
下表列出 Azure 監視器中目前可用的代理程式資料來源。  每個資料來源都有個別的文章連結，提供該資料來源的詳細資料。   此外，還提供收集方法與頻率的相關資訊。 


| 資料來源 | 平台 | Log analytics 代理程式 | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [自訂的記錄](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 與抵達同時 |
| [自訂的記錄](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 與抵達同時 |
| [IIS 記錄檔](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |取決於記錄檔檔案換用設定 |
| [效能計數器](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |依排程，最少 10 秒 |
| [效能計數器](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |依排程，最少 10 秒 |
| [syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |從 Azure 儲存體 ：10 分鐘；從代理程式：與抵達同時 |
| [Windows 事件記錄檔](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 與抵達同時 |


## <a name="configuring-data-sources"></a>設定資料來源
您可以從工作區其 [進階設定]**** 中的 [資料]**** 功能表設定資料來源。  系統會將任何設定傳遞到工作區中所有已連接的來源。  您目前無法透過這項組態來排除任何代理程式。

![設定 Windows 事件](media/agent-data-sources/configure-events.png)

1. 在 Azure 入口網站中，選取 [Log Analytics 工作區]**** > 您的工作區 > [進階設定]****。
2. 選取 [**資料**]。
3. 按一下要設定的資料來源。
4. 參閱上表中每個資料來源的文件連結，即可取得其組態的詳細資訊。


## <a name="data-collection"></a>資料集合
系統會在幾分鐘內，將資料來源組態傳遞給直接連接到 Azure 監視器的代理程式。  系統會從代理程式收集指定的資料，並依據每個資料來源特定的時間間隔，直接傳遞給 Azure 監視器。  請參閱每個資料來源的文件，以了解這些特性。

若是已連接管理群組中的 System Center Operations Manager 代理程式，系統會將資料來源組態轉譯為管理組件，並依預設每隔 5 分鐘傳遞給管理群組。  如同其他任何代理程式一樣，此代理程式也會下載管理組件，並收集指定的資料。 視資料來源而定，資料會傳送至管理伺服器，而管理伺服器再將資料轉送至 Azure 監視器，或者，由這個代理程式直接將資料傳送至 Azure 監視器，而沒有經過管理伺服器。 如需詳細資訊，請參閱 [Azure 中監視解決方案的資料收集詳細資料](../insights/solutions-inventory.md)。  您可以閱讀連接 Operations Manager 與 Azure 監視器的詳細資訊，以及修改在[設定與 System Center Operations Manager 的整合](om-agents.md)時傳遞之組態頻率的詳細資訊。

如果代理程式無法連接到 Azure 監視器或 Operations Manager，它將會繼續收集資料，以便在建立連線時進行傳遞。  如果資料量達到用戶端的快取大小上限，或者代理程式無法在 24 小時內建立連線，可能就會遺失資料。

## <a name="log-records"></a>所有記錄
Azure 監視器收集的所有記錄資料都會以記錄形式儲存在工作區中。  不同資料來源所收集的記錄會有自己的一組屬性，並由其 **類型** 屬性來識別。  如需每種記錄類型的詳細資訊，請參閱各資料來源和方案的文件。

## <a name="next-steps"></a>後續步驟
* 了解可將功能新增至 Azure 監視器，並會將資料收集到工作區的[監視解決方案](../insights/solutions.md)。
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和監視解決方案收集到的資料。  
* 設定[警示](alerts-overview.md)，以便從資料來源和監視解決方案收集到重要資料時主動通知您。
