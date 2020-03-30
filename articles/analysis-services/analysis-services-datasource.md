---
title: Azure Analysis Services 中支援的資料來源 | Microsoft Docs
description: 描述 Azure 分析服務中表式 1200 和更高資料模型支援的資料來源和連接器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461652"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支援的資料來源

Azure 分析服務和 SQL 伺服器分析服務都顯示了 Azure 分析服務和 SQL 伺服器分析服務中顯示的資料來源和連接器，這些顯示在視覺化工作室中獲取資料或表導入嚮導以及分析服務專案。 但是，Azure 分析服務中並非所有顯示的資料來源和連接器都支援。 可以連接的資料來源類型取決於許多因素，如模型相容性級別、可用資料連線器、身份驗證類型和本地資料閘道支援。 下表描述了 Azure 分析服務的支援資料來源。

## <a name="azure-data-sources"></a>Azure 資料來源

|資料來源  |記憶體內  |DirectQuery  |注意 |
|---------|---------|---------|---------|
|Azure SQL Database      |   是      |    是      |<sup>[2](#azprovider)</sup>， <sup> [3](#azsqlmanaged)</sup>|
|Azure 突觸分析（SQL 資料倉儲）      |   是      |   是       |<sup>[2](#azprovider)</sup>|
|Azure Blob 儲存體      |   是       |    否      | <sup>[1](#tab1400a)</sup> |
|Azure 表格儲存體     |   是       |    否      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  是        |  否        |<sup>[1](#tab1400a)</sup> |
|Azure 資料湖存儲第 1 代      |   是       |    否      |<sup>[1](#tab1400a)</sup> |
|Azure 資料湖存儲第 2 代       |   是       |    否      |<sup>[1](#tab1400a)</sup>， <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     是     |   否       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   是       |   否       |<sup>[1](#tab1400a)</sup>， <sup> [4](#databricks)</sup>|
||||

**筆記：**   
<a name="tab1400a">1</a> - 僅限 Tabular 1400 和更高模型。  
<a name="azprovider">2</a> - 當指定為表格 1200 和更高模型中的*提供程式*資料來源時，記憶體中和 DirectQuery 模型都需要用於 SQL Server MSOLEDBSQL 的 Microsoft OLE DB 驅動程式（推薦）、SQL Server 本機用戶端 11.0 或用於 SQL Server 的 .NET 框架資料提供程式。    
<a name="azsqlmanaged">3</a> - 支援 Azure SQL 資料庫託管實例。 由於託管實例在 Azure VNet 中使用私人 IP 位址運行，因此必須在實例上啟用公共終結點。 如果未啟用，則需要[本地資料閘道](analysis-services-gateway.md)。    
<a name="databricks">4</a> - 當前不支援使用 Spark 連接器的 Azure 資料磚。   
<a name="gen2">5</a> - ADLS Gen2 連接器目前不受支援，但是，Azure Blob 存儲連接器可以與 ADLS Gen2 資料來源一起使用。   

## <a name="other-data-sources"></a>其他資料來源

|資料來源 | 記憶體內 | DirectQuery |注意   |
|  --- | --- | --- | --- |
|Access 資料庫     |  是 | 否 |  |
|Active Directory     |  是 | 否 | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  是 | 否 |  |
|分析平台系統     |  是 | 否 |  |
|CSV 檔案  |是 | 否 |  |
|Dynamics 365     |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|Excel 活頁簿     |  是 | 否 |  |
|Exchange      |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|資料夾      |是 | 否 | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |是 | 否 |  |
|JSON 文件      |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|二進位的行      | 是 | 否 | <sup>[6](#tab1400b)</sup> |
|MySQL Database     | 是 | 否 |  |
|OData 摘要      |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|ODBC 查詢     | 是 | 否 |  |
|OLE DB     |   是 | 否 |  |
|Oracle  | 是  |是  | <sup>[9](#oracle)</sup> |
|PostgreSQL 資料庫   | 是 | 否 | <sup>[6](#tab1400b)</sup> |
|Salesforce 物件|  是 | 否 | <sup>[6](#tab1400b)</sup> |
|Salesforce 報表 |是 | 否 | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  是 | 否 |  |
|SAP Business Warehouse    |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|SharePoint 清單      |   是 | 否 | <sup>[6](#tab1400b)</sup>， <sup> [11](#filesSP)</sup> |
|SQL Server |是   | 是  | <sup>[7](#sqlim)</sup>， <sup> [8](#instgw)</sup> | 
|SQL Server 資料倉儲 |是   | 是  | <sup>[7](#sqlim)</sup>， <sup> [8](#instgw)</sup> |
|Sybase 資料庫     |  是 | 否 |  |
|Teradata | 是  | 是  | <sup>[10](#teradata)</sup> |
|TXT 檔  |是 | 否 |  |
|XML 表    |  是 | 否 | <sup>[6](#tab1400b)</sup> |
| | | |

**筆記：**   
<a name="tab1400b">6</a> - 僅表格 1400 及以上型號。  
<a name="sqlim">7</a> - 在表格 1200 和更高模型中指定為*提供程式*資料來源時，為 SQL Server MSOLEDBSQL（推薦）、SQL Server 本機用戶端 11.0 或 SQL Server 的 .NET 框架資料提供程式指定 Microsoft OLE DB 驅動程式。  
<a name="instgw">8</a> - 如果指定 MSOLEDBSQL 作為資料提供程式，則可能需要在與本地資料閘道相同的電腦上下載並安裝 SQL Server 的[Microsoft OLE DB 驅動程式](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server)。  
<a name="oracle">9</a> - 對於表格 1200 模型，或作為表格 1400+ 模型中的*提供程式*資料來源，請為 .NET 指定 Oracle 資料提供程式。  
<a name="teradata">10</a> - 對於表格 1200 模型，或作為表格 1400+ 模型中的*提供程式*資料來源，為 .NET 指定 Teradata 資料提供程式。   
<a name="filesSP">11</a> - 不支援本地 SharePoint 中的檔。

從 Azure 分析服務伺服器連接到本地資料來源需要[本地閘道](analysis-services-gateway.md)。 使用閘道時，需要 64 位元的提供者。 

## <a name="understanding-providers"></a>瞭解提供程式

在 Visual Studio 中創建表格 1400 和更高模型專案時，預設情況下，使用**Get Data**連接到資料來源時，不會指定資料提供程式。 表格 1400 和更高型號使用[Power Query](/power-query/power-query-what-is-power-query)連接器來管理資料來源和分析服務之間的連接、資料查詢和混搭。 這些有時稱為該連接中*為*您設置的結構化資料來源連接。 但是，您可以啟用舊資料源。 啟用後，可以使用**表導入嚮導**連接到表 1200 中傳統上支援的某些資料來源，將較低模型作為*舊版*或*提供程式*資料來源進行連接。 指定為提供程式資料來源時，可以指定特定資料提供程式和其他高級連接屬性。 例如，您可以將連接到本地 SQL Server 資料倉儲，甚至將 Azure SQL 資料庫作為舊資料源進行連接。 然後，您可以選擇 SQL Server MSOLEDBSQL 資料提供程式的 OLE DB 驅動程式。 在這種情況下，選擇 OLE DB 資料提供程式可能會通過電源查詢連接器提供更高的性能。 

在視覺化工作室中使用表導入嚮導時，連接到任何資料來源都需要資料提供程式。 為您選擇預設資料提供程式。 如果需要，可以更改資料提供程式。 您選擇的提供程式類型取決於性能、模型是否使用記憶體中存儲或 DirectQuery，以及將模型部署到哪個分析服務平臺。

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>在表格 1400 和更高模型專案中指定提供程式資料來源

要啟用提供程式資料來源，請在 Visual Studio 中按一下 **"工具** > **選項** > **分析服務表格資料** > **導入**"，選擇**啟用舊資料源**。

![啟用舊資料源](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

啟用舊資料源後，在**表格模型資源管理器**中，**按右鍵** > **資料來源從資料來源導入（舊版）。**

![表格模型資源管理器中的舊資料源](media/analysis-services-datasource/aas-import-legacy-datasources.png)

與表格 1200 模型專案一樣，使用**表導入嚮導**連接到資料來源。 在連接頁上，按一下 **"高級**"。 在 **"設置高級屬性"** 中指定資料提供程式和其他連接設置。

![舊資料源 高級屬性](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>模擬
在某些情況下，可能需要指定不同的模擬帳戶。 可以在 Visual Studio 或 SSMS 中指定類比帳戶。

對於內部部署資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。
* 如果使用 Windows 驗證，請設定 Windows 使用者/密碼。 對於 SQL Server，In-Memory 資料模型僅支援具有特定模擬帳戶的 Windows 驗證。

對於雲端資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。

## <a name="oauth-credentials"></a>OAuth 憑據

對於使用記憶體中模式的 1400 和更高相容性級別的表格模型，Azure SQL 資料庫、Azure 突觸分析（SQL 資料倉儲）、動態 365 和 SharePoint 清單支援 OAuth 憑據。 Azure 分析服務管理 OAuth 資料來源的權杖刷新，以避免長時間運行的刷新操作超時。 要生成有效的權杖，請使用 SSMS 設置憑據。

OAuth 憑據不支援直接查詢模式。

## <a name="next-steps"></a>後續步驟
[本地閘道](analysis-services-gateway.md)   
[管理您的伺服器](analysis-services-manage.md)   

