---
title: Azure Analysis Services 中支援的資料來源 | Microsoft Docs
description: 描述 Azure Analysis Services 中表格式1200和更高資料模型支援的資料來源和連接器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77461652"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支援的資料來源

針對 Azure Analysis Services 和 SQL Server Analysis Services，會顯示 [取得資料] 或 [資料表匯入嚮導] 中顯示的資料來源和連接器，其中包含 Analysis Services 專案的 Visual Studio。 不過，Azure Analysis Services 不支援所有顯示的資料來源和連接器。 您可連接的資料來源類型取決於許多因素，例如模型相容性層級、可用的資料連線器、驗證類型，以及內部部署資料閘道支援。 下表說明 Azure Analysis Services 支援的資料來源。

## <a name="azure-data-sources"></a>Azure 資料來源

|資料來源  |記憶體內  |DirectQuery  |備忘錄 |
|---------|---------|---------|---------|
|Azure SQL Database      |   是      |    是      |<sup>[2](#azprovider)</sup>、 <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL 資料倉儲)      |   是      |   是       |<sup>[2](#azprovider)</sup>|
|Azure Blob 儲存體      |   是       |    否      | <sup>[sha-1](#tab1400a)</sup> |
|Azure 表格儲存體     |   是       |    否      | <sup>[sha-1](#tab1400a)</sup>|
|Azure Cosmos DB     |  是        |  否        |<sup>[sha-1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   是       |    否      |<sup>[sha-1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   是       |    否      |<sup>[1](#tab1400a)</sup>、 <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     是     |   否       |<sup>[sha-1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   是       |   否       |<sup>[1](#tab1400a)</sup>、 <sup> [4](#databricks)</sup>|
||||

**紀錄**   
<a name="tab1400a">1</a> - 僅限 Tabular 1400 和更高模型。  
<a name="azprovider">2</a> -當指定為表格式1200和更高模型中的*提供者*資料來源時，記憶體內部和 DirectQuery 模型都需要 Microsoft OLE DB Driver for SQL Server 內含 msoledbsql.h （建議）、SQL Server Native Client 11.0，或 Data Provider 的 .NET Framework SQL Server。    
<a name="azsqlmanaged">3</a> -支援 Azure SQL Database 受控執行個體。 因為受控實例會在具有私人 IP 位址的 Azure VNet 中執行，所以必須在實例上啟用公用端點。 如果未啟用，則需要內部[部署資料閘道](analysis-services-gateway.md)。    
<a name="databricks">4</a> -目前不支援使用 Spark 連接器 Azure Databricks。   
<a name="gen2">5</a> -ADLS Gen2 連接器目前不受支援，不過，Azure Blob 儲存體連接器可以與 ADLS Gen2 資料來源搭配使用。   

## <a name="other-data-sources"></a>其他資料來源

|資料來源 | 記憶體內 | DirectQuery |備忘錄   |
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
|二進位檔中的行      | 是 | 否 | <sup>[6](#tab1400b)</sup> |
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
|SharePoint 清單      |   是 | 否 | <sup>[6](#tab1400b)</sup>、 <sup> [11](#filesSP)</sup> |
|SQL Server |是   | 是  | <sup>[7](#sqlim)</sup>、 <sup> [8](#instgw)</sup> | 
|SQL Server 資料倉儲 |是   | 是  | <sup>[7](#sqlim)</sup>、 <sup> [8](#instgw)</sup> |
|Sybase 資料庫     |  是 | 否 |  |
|Teradata | 是  | 是  | <sup>[10](#teradata)</sup> |
|TXT 檔案  |是 | 否 |  |
|XML 資料表    |  是 | 否 | <sup>[6](#tab1400b)</sup> |
| | | |

**紀錄**   
<a name="tab1400b">6</a> -僅限表格式1400和更高的模型。  
<a name="sqlim">7</a> -當指定為表格式1200和更高模型中的*提供者*資料來源時，請為 Data Provider 指定 SQL Server 內含 msoledbsql.h （建議）的 Microsoft OLE DB 驅動程式、SQL Server Native Client 11.0 或 .NET Framework SQL Server。  
<a name="instgw">8</a> -如果將內含 msoledbsql.h 指定為數據提供者，可能需要在與內部部署資料閘道相同的電腦上，下載並安裝[適用于 SQL Server 的 Microsoft OLE DB 驅動程式](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server)。  
<a name="oracle">9</a> -若為表格式1200模型，或表格式 1400 + 模型中的*提供者*資料來源，請指定 .net 的 Oracle Data Provider。  
<a name="teradata">10</a> -若為表格式1200模型，或為表格式 1400 + 模型中的*提供者*資料來源，請指定 .net 的 Teradata Data Provider。   
<a name="filesSP">11</a> -不支援在內部部署 SharePoint 中的檔案。

從 Azure Analysis Services 伺服器連接到內部部署資料來源需要內部[部署閘道](analysis-services-gateway.md)。 使用閘道時，需要 64 位元的提供者。 

## <a name="understanding-providers"></a>瞭解提供者

在 Visual Studio 中建立表格式1400和更高版本的模型專案時，根據預設，當您使用 [**取得資料**] 連接到資料來源時，不會指定資料提供者。 表格式1400和更新版本的模型會使用[Power Query](/power-query/power-query-what-is-power-query)連接器來管理資料來源與 Analysis Services 之間的連接、資料查詢和混搭程式。 這些在連接屬性設定中，有時稱為*結構化*資料來源連接。 不過，您可以啟用舊版資料來源。 啟用時，您可以使用 [**資料表匯入嚮導**]，連接到傳統上支援1200的某些資料來源，以及*舊版*或*提供者*資料來源的較低模型。 當指定為提供者資料來源時，您可以指定特定的資料提供者和其他的 advanced 連接屬性。 例如，您可以連接到內部部署 SQL Server 資料倉儲，或甚至 Azure SQL Database 作為舊版資料來源。 然後，您可以選取 SQL Server 內含 MSOLEDBSQL.H 資料提供者的 OLE DB 驅動程式。 在此情況下，選取 OLE DB 資料提供者可能會透過 Power Query 連接器來提供改善的效能。 

在 Visual Studio 中使用 [資料表匯入] 時，與任何資料來源的連接都需要資料提供者。 系統會為您選取預設的資料提供者。 如有需要，您可以變更資料提供者。 您選擇的提供者類型可能取決於效能、模型是否使用記憶體內部儲存體或 DirectQuery，以及您要將模型部署到哪個 Analysis Services 平臺。

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>在表格式1400和更高的模型專案中指定提供者資料來源

若要啟用提供者資料來源，請在 Visual Studio 中，按一下 [**工具** > ] [**選項** > ] [**Analysis Services 表格式** > **資料匯入**]，選取 [**啟用舊版資料**

![啟用舊版資料來源](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

啟用舊版資料來源之後，在**表格式模型 Explorer**中，以滑鼠**Data Sources** > 按右鍵 **[從資料來源匯入資料來源（舊版）**]。

![表格式模型瀏覽器中的舊版資料來源](media/analysis-services-datasource/aas-import-legacy-datasources.png)

就像表格式1200模型專案一樣，使用 [**資料表匯入嚮導**] 來連接到資料來源。 在 [連接] 頁面上，按一下 [ **Advanced**]。 在 [設定] [**高級屬性**] 中指定資料提供者和其他連接設定。

![舊版資料來源的 Advanced 屬性](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>模擬
在某些情況下，可能需要指定不同的模擬帳戶。 可以在 Visual Studio 或 SSMS 中指定模擬帳戶。

對於內部部署資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。
* 如果使用 Windows 驗證，請設定 Windows 使用者/密碼。 對於 SQL Server，In-Memory 資料模型僅支援具有特定模擬帳戶的 Windows 驗證。

對於雲端資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。

## <a name="oauth-credentials"></a>OAuth 認證

針對1400和更高相容性層級使用記憶體中模式的表格式模型，Azure SQL Database、Azure Synapse 分析（SQL 資料倉儲）、Dynamics 365 和 SharePoint 清單支援 OAuth 認證。 Azure Analysis Services 會管理 OAuth 資料來源的權杖重新整理，以避免長時間執行的重新整理作業發生超時。 若要產生有效的權杖，請使用 SSMS 設定認證。

OAuth 認證不支援直接查詢模式。

## <a name="next-steps"></a>後續步驟
[內部部署閘道](analysis-services-gateway.md)   
[管理您的伺服器](analysis-services-manage.md)   

