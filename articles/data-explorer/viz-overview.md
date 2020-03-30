---
title: Azure 資料資源管理器資料視覺化
description: 瞭解視覺化 Azure 資料資源管理器資料的不同方式
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139057"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 資料資源管理器進行資料視覺化 

Azure 資料資源管理器是一種用於日誌和遙測資料的快速且高度可擴展的資料探索服務，用於為大量資料構建複雜的分析解決方案。 Azure 資料資源管理器與各種視覺化檢視集成，因此您可以視覺化資料並在整個組織中共用結果。 這些資料可以轉換為可操作的見解，對您的業務產生影響。

資料視覺化和報告是資料分析過程中的關鍵步驟。 Azure 資料資源管理器支援許多 BI 服務，因此可以使用最適合方案和預算的服務。

## <a name="kusto-query-language-visualizations"></a>庫斯托查詢語言視覺化

Kusto 查詢語言[`render operator`](/azure/kusto/query/renderoperator)提供各種視覺化效果，如表格、圓形圖和橫條圖，用於描述查詢結果。 查詢視覺化有助於異常檢測和預測、機器學習等。

## <a name="power-bi"></a>Power BI

Azure 資料資源管理器提供了使用各種方法連接到[Power BI](https://powerbi.microsoft.com)的功能： 

  * [內置本機電源 BI 連接器](/azure/data-explorer/power-bi-connector)

  * [從 Azure 資料資源管理器導入電源 BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 查詢](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 資料資源管理器提供使用[內置本機 Excel 連接器](excel-connector.md)連接到[Microsoft Excel](https://products.office.com/excel)的功能，或將查詢從 Azure 資料資源管理器[導入](excel-blank-query.md)到 Excel 中。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com)提供了一個 Azure 資料資源管理器外掛程式，使您能夠視覺化來自 Azure 資料資源管理器的資料。 [將 Azure 資料資源管理器設置為 Grafana 的資料來源，然後視覺化資料](/azure/data-explorer/grafana)。 

## <a name="kibana"></a>Kibana

Azure 資料資源管理器提供使用開源連接器 K2Bridge 連接到[Kibana（發現頁面）](https://www.elastic.co/guide/en/kibana/6.8/discover.html)的功能。 [將 Azure 資料資源管理器設置為 Kibana 的資料來源，然後視覺化資料](/azure/data-explorer/k2bridge)。

## <a name="odbc-connector"></a>ODBC 連接器

Azure 資料資源管理器提供[開放資料庫連接 （ODBC） 連接器](connect-odbc.md)，因此任何支援 ODBC 的應用程式都可以連接到 Azure 資料資源管理器。

## <a name="tableau"></a>Tableau

Azure 資料資源管理器提供了使用[ODBC 連接器](/azure/data-explorer/connect-odbc)連接到[Tableau](https://www.tableau.com)的功能，然後在[Tableau 中視覺化資料](tableau.md)。

## <a name="qlik"></a>Qlik

Azure 資料資源管理器提供了使用[ODBC 連接器](/azure/data-explorer/connect-odbc)連接到[Qlik](https://www.qlik.com)的功能，然後創建 Qlik Sense 儀表板並視覺化資料。 使用以下視頻，可以學習使用 Qlik 視覺化 Azure 資料資源管理器資料。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 資料資源管理器提供了使用 JDBC 連接器連接到[Sisense](https://www.sisense.com)的功能。 [將 Azure 資料資源管理器設置為 Sisense 的資料來源，然後視覺化資料](/azure/data-explorer/sisense)。

## <a name="redash"></a>Redash

您可以使用[Redash](https://redash.io/)生成儀表板和視覺化資料。 [將 Azure 資料資源管理器設置為 Redash 的資料來源，然後視覺化資料](/azure/data-explorer/redash)。