---
title: Azure Analysis Services 中支援的資料來源 | Microsoft Docs
description: 描述 Azure Analysis Services 中表格式 1200 和更高資料模型支援的資料來源和連接器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b778cf55ea485d7b3b4d3730d3659750f27b2697
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685590"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支援的資料來源

在 Visual Studio [取得資料精靈] 或 [資料表匯入精靈] 中顯示的 Analysis Services 專案資料來源和連接器，也會在 Azure Analysis Services 和 SQL Server Analysis Services 中顯示。 不過，這不表示 Azure Analysis Services 支援所有顯示的資料來源和連接器。 您可連接的資料來源類型取決於多個因素，例如模型相容性層級、可用的資料連接器、驗證類型，以及內部部署資料閘道支援。 下表描述 Azure Analysis Services 支援的資料來源。

## <a name="azure-data-sources"></a>Azure 資料來源

|資料來源  |記憶體內  |DirectQuery  |注意 |
|---------|---------|---------|---------|
|Azure SQL Database      |   是      |    是      |<sup>[2](#azprovider)</sup>、<sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   是      |   是       |<sup>[2](#azprovider)</sup>|
|Azure Blob 儲存體      |   是       |    否      | <sup>[1](#tab1400a)</sup> |
|Azure 表格儲存體     |   是       |    否      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  是        |  否        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   是       |    否      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   是       |    否      |<sup>[1](#tab1400a)</sup>、<sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     是     |   否       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   是       |   否       |<sup>[1](#tab1400a)</sup>、<sup>[4](#databricks)</sup>|
||||

**注意：**

<a name="tab1400a">1</a> - 僅限 Tabular 1400 和更高模型。  
<a name="azprovider">2</a> - 在表格式 1200 和更高模型中指定為「提供者」資料來源時，記憶體內部和 DirectQuery 模型都需要 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (建議)、SQL Server Native Client 11.0 或 .NET Framework Data Provider for SQL Server。  
<a name="azsqlmanaged">3</a> -支援 Azure SQL 受控執行個體。 因為 SQL 受控執行個體是在具有私人 IP 位址的 Azure VNet 內執行，所以必須在實例上啟用公用端點。 如果未啟用，則需要[內部部署資料閘道](analysis-services-gateway.md)。  
<a name="databricks">4</a> - 目前不支援使用 Spark 連接器的 Azure Databricks。  
<a name="gen2">5</a> - 目前不支援 ADLS Gen2 連接器，但 Azure Blob 儲存體連接器可與 ADLS Gen2 資料來源搭配使用。

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
|二進位檔中的程式行      | 是 | 否 | <sup>[6](#tab1400b)</sup> |
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
|SharePoint 清單      |   是 | 否 | <sup>[6](#tab1400b)</sup>、<sup>[11](#filesSP)</sup> |
|SQL Server |是   | 是  | <sup>[7](#sqlim)</sup>、<sup>[8](#instgw)</sup> |
|SQL Server 資料倉儲 |是   | 是  | <sup>[7](#sqlim)</sup>、<sup>[8](#instgw)</sup> |
|Sybase 資料庫     |  是 | 否 |  |
|Teradata | 是  | 是  | <sup>[10](#teradata)</sup> |
|TXT 檔案  |是 | 否 |  |
|XML 表格    |  是 | 否 | <sup>[6](#tab1400b)</sup> |
| | | |

**注意：**  
<a name="tab1400b">6</a> - 僅限表格式 1400 和更高模型。  
<a name="sqlim">7</a> - 在表格式 1200 和更高模型中指定為「提供者」資料來源時，請指定 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (建議)、SQL Server Native Client 11.0 或 .NET Framework Data Provider for SQL Server。  
<a name="instgw">8</a> - 如果指定 MSOLEDBSQL 作為資料提供者，則可能需要在與內部部署資料閘道相同的電腦上，下載並安裝 [Microsoft OLE DB Driver for SQL Server](/sql/connect/oledb/oledb-driver-for-sql-server)。  
<a name="oracle">9</a> - 您可針對表格式 1200 模型指定 Oracle Data Provider for .NET，或將其指定為表格式 1400+ 模型中的「提供者」資料來源。 如果指定為結構化資料來源，請務必 [啟用 Oracle managed provider](#enable-oracle-managed-provider)。   
<a name="teradata">10</a> - 您可針對表格式 1200 模型指定 Teradata Data Provider for .NET，或將其指定為表格式 1400+ 模型中的「提供者」資料來源。  
<a name="filesSP">11</a> - 不支援內部部署 SharePoint 中的檔案。

從 Azure Analysis Services 伺服器連接到內部部署資料來源需要[內部部署閘道](analysis-services-gateway.md)。 使用閘道時，需要 64 位元的提供者。

## <a name="understanding-providers"></a>了解提供者

在 Visual Studio 中建立表格式 1400 和更高模型專案時，預設不會在使用 [取得資料] 連接到資料來源時指定資料提供者。 表格式 1400 和更高模型會使用 [Power Query](/power-query/power-query-what-is-power-query) 連接器來管理資料來源與 Analysis Services 之間的連接、資料查詢和混搭。 這有時候稱為「結構化」資料來源連接，其會在連接屬性設定中設定。 不過，您可在 Visual Studio 中啟用模型專案的舊版資料來源。 啟用時，您可使用 [資料表匯入精靈] 來連接到表格式 1200 和更低模型傳統上支援作為「舊版」或「提供者」資料來源的特定資料來源。 指定為提供者資料來源時，您可指定特定資料提供者及其他進階連接屬性。 例如，您可以連接到 SQL Server 資料倉儲實例，甚至 Azure SQL Database 作為舊版資料來源。 然後，您可選取 OLE DB Driver for SQL Server MSOLEDBSQL 資料提供者。 在此情況下，選取 OLE DB 資料提供者可透過 Power Query 連接器來提供改善的效能。 

在 Visual Studio 中使用 [資料表匯入精靈] 時，與任何資料來源的連接都需要資料提供者。 系統會選取預設資料提供者。 您可視需要變更資料提供者。 您選擇的提供者類型可能取決於效能、模型是否使用記憶體內部儲存體或 DirectQuery，以及要將模型部署到哪個 Analysis Services 平台。

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>在表格式 1400 和更高模型專案中指定提供者資料來源

若要啟用提供者資料來源，請在 Visual Studio 中，按一下 [工具] > [選項] > [Analysis Services 表格式] > [資料匯入]，然後選取 [啟用舊版資料來源]。

![啟用舊版資料來源](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

啟用舊版資料來源時，在 [表格式模型總管] 中，以滑鼠右鍵按一下 [資料來源] > [從資料來源匯入 (舊版)]。

![[表格式模型總管] 中的舊版資料來源](media/analysis-services-datasource/aas-import-legacy-datasources.png)

如同表格式 1200 模型專案，使用 [資料表匯入精靈] 來連接到資料來源。 在 [連接] 頁面上，按一下 [進階]。 在 [設定進階屬性] 中指定資料提供者及其他連接設定。

![舊版資料來源的 [進階] 屬性](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>模擬
在某些情況下，可能需要指定不同的模擬帳戶。 您可在 Visual Studio 或 SQL Server Management Studio (SSMS) 中指定模擬帳戶。

對於內部部署資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。
* 如果使用 Windows 驗證，請設定 Windows 使用者/密碼。 對於 SQL Server，In-Memory 資料模型僅支援具有特定模擬帳戶的 Windows 驗證。

對於雲端資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。

## <a name="service-principal-authentication"></a>服務主體驗證

當指定為 *提供者* 資料來源時，azure Analysis Services 支援 Azure SQL Database 和 Azure Synapse 資料來源的 [msoledbsql.h](/sql/connect/oledb/release-notes-for-oledb-driver-for-sql-server) Azure Active Directory 服務主體驗證。

`
Provider=MSOLEDBSQL;Data Source=[server];Initial Catalog=[database];Authentication=ActiveDirectoryServicePrincipal;User ID=[Application (client) ID];Password=[Application (client) secret];Use Encryption for Data=true
`

## <a name="oauth-credentials"></a>OAuth 認證

若為1400和更高相容性層級的表格式模型（使用記憶體內部模式），Azure SQL Database、Azure Synapse、Dynamics 365 和 SharePoint 清單支援 OAuth 認證。 Azure Analysis Services 會管理 OAuth 資料來源的權杖重新整理，以避免長時間執行的重新整理作業逾時。 若要產生有效的權杖，請使用 Power Query 設定認證。

OAuth 認證不支援 DirectQuery 模式。

## <a name="enable-oracle-managed-provider"></a>啟用 Oracle managed 提供者

在某些情況下，對 Oracle 資料來源的 DAX 查詢可能會傳回非預期的結果。 這可能是因為資料來源連接所使用的提供者。

如「 [瞭解提供者](#understanding-providers) 」一節所述，表格式模型會以 *結構化* 資料來源或 *提供者* 資料來源的形式連接到資料來源。 針對已指定為提供者資料來源之 Oracle 資料來源的模型，請確定指定的提供者為 .NET (DataAccess 的 Oracle Data Provider。用戶端) 。 

如果 Oracle 資料來源已指定為結構化資料來源，請啟用 **MDataEngine\UseManagedOracleProvider** 伺服器屬性。 設定這個屬性可確保您的模型使用 .NET managed 提供者的建議 Oracle Data Provider 連接到 Oracle 資料來源。
 
若要啟用 Oracle managed 提供者：

1. 在 SQL Server Management Studio 中，連接到您的伺服器。
2. 使用下列腳本建立 XMLA 查詢。 以完整伺服器名稱取代 **ServerName** ，然後執行查詢。

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. 重新啟動伺服器。


## <a name="next-steps"></a>後續步驟

* [內部部署閘道](analysis-services-gateway.md)
* [管理您的伺服器](analysis-services-manage.md)