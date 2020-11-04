---
title: 使用 BI 分析工具連線到 Azure Cosmos DB
description: 了解如何使用 Azure Cosmos DB ODBC 驅動程式建立資料表和檢視，使標準化資料可以在 BI 以及資料分析軟體中檢視。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339779"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>使用 BI 分析工具搭配 ODBC 驅動程式連線到 Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ODBC 驅動程式可讓您使用 BI 分析工具 (例如 SQL Server 整合服務、Power BI Desktop 和 Tableau) 連線到 Azure Cosmos DB，讓您可以在那些解決方案中分析和建立您的 Azure Cosmos DB 資料視覺效果。

Azure Cosmos DB ODBC 驅動程式符合 ODBC 3.8 規範，且支援 ANSI SQL-92 語法。 該驅動程式提供豐富的功能，可協助您重新標準化 Azure Cosmos DB 中的資料。 您可以使用驅動程式將 Azure Cosmos DB 中的資料以資料表或檢視來表示。 該驅動程式可讓您針對資料表和檢視執行 SQL 作業，包括依查詢、插入、更新和刪除分組。

> [!NOTE]
> 目前僅支援 Azure Cosmos DB SQL API 帳戶的 Azure Cosmos DB 與 ODBC 驅動程式連線。

## <a name="why-do-i-need-to-normalize-my-data"></a>為什麼需要將我的資料標準化？
Azure Cosmos DB 是無結構描述的資料庫，可以快速開發應用程式，並能夠在不受限於嚴格的結構描述的情況下，逐一查看資料模型。 單一 Azure Cosmos 資料庫可以包含各種結構的 JSON 檔。 這非常適合快速應用程式開發，但是當您想要使用資料分析和 BI 工具分析和建立資料報告時，資料通常都需要壓平合併，並遵循特定的結構描述。

這時就需要 ODBC 驅動程式。 透過使用 ODBC 驅動程式，您現在便可將 Azure Cosmos DB 中的資料重新標準化為符合您資料分析與報告需求的資料表和檢視。 重新標準化的結構描述不會對基礎資料造成影響，也不會限制開發人員必須遵循它們。 相反地，它們使您能夠利用符合 ODBC 規範的工具來存取資料。 因此，現在您的 Azure Cosmos 資料庫不只是您的開發小組的最愛，但您的資料分析師也會喜歡。

讓我們開始使用 ODBC 驅動程式。

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>步驟 1︰安裝 Azure Cosmos DB ODBC 驅動程式

1. 下載適用於您的環境的驅動程式：

    | 安裝程式 | 支援的作業系統| 
    |---|---| 
    |適用於 64 位元 Windows 的 [Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64)| 64 位元版本的 Windows 8.1 或更新版本、Windows 8、Windows 7、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2。| 
    |適用於 32 位元和 64 位元 Windows 的 [Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64)| 64 位元版本的 Windows 8.1 或更新版本、Windows 8、Windows 7、Windows XP、Windows Vista、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 和 Windows Server 2003。| 
    |適用於 32 位元 Windows 的 [Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32)|32 位元版本的 Windows 8.1 或更新版本、Windows 8、Windows 7、Windows XP 和 Windows Vista。|

    在本機執行 msi 檔案，它會啟動 **Microsoft Azure Cosmos DB ODBC 驅動程式安裝精靈** 。 

1. 使用預設的輸入安裝 ODBC 驅動程式，以完成安裝精靈。

1. 在您的電腦上開啟 [ODBC 資料來源管理員] 應用程式。 您可以透過在 Windows 搜尋方塊中輸入 **ODBC 資料來源** 來執行此操作。 
    您可以按一下 [驅動程式] 索引標籤來確認驅動程式已安裝，並確定已列出 **Microsoft Azure Cosmos DB ODBC 驅動程式** 。

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Azure Cosmos DB ODBC 資料來源管理員":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>步驟2：連接到您的 Azure Cosmos 資料庫

1. [安裝 Azure Cosmos DB ODBC 驅動程式](#install)之後，在 [ODBC 資料來源管理員] 視窗中，按一下 [新增]。 您可以建立「使用者 DSN」或「系統 DSN」。 在此範例中，您會建立「使用者 DSN」。

1. 在 [建立新的資料來源] 視窗中，選取 [Microsoft Azure Cosmos DB ODBC 驅動程式]，然後按一下 [完成]。

1. 在 [Azure Cosmos DB ODBC 驅動程式 SDN 設定] 視窗中，填寫下列資訊︰ 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Azure Cosmos DB ODBC 驅動程式 DSN 設定視窗":::
    - **資料來源名稱** ：您的 ODBC DSN 易記名稱。 此名稱專屬於您的 Azure Cosmos DB 帳戶，因此如果您有多個帳戶，請適當命名。
    - **描述** ：資料來源的簡短描述。
    - **主機** ：Azure Cosmos DB 帳戶的 URI。 您可以從 Azure 入口網站中的 [Azure Cosmos DB 金鑰] 頁面擷取此資訊，如下列螢幕擷取畫面所示。 
    - **存取金鑰** ︰來自 Azure 入口網站中 [Azure Cosmos DB 金鑰] 頁面的金鑰，分為主要或次要、讀寫或唯讀金鑰，如下列螢幕擷取畫面所示。 如果 DSN 是針對唯讀資料處理和報告使用，建議您使用唯讀金鑰。
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Azure Cosmos DB 金鑰頁面":::
    - **加密存取金鑰** ：根據此電腦的使用者選取最佳選項。 
    
1. 按一下 [測試] 按鈕，確定您可以連線到您的 Azure Cosmos DB 帳戶。 

1.  按一下 [進階選項] 並設定下列值︰
    *  **REST API 版本** ：選取作業的 [REST API 版本](/rest/api/cosmos-db/) 。 預設值為2015-12-16。 如果您的容器具有 [大型分割](large-partition-keys.md) 區索引鍵，且需要 REST API 2018-12-31 版：
        - 針對 REST API 版本輸入 **2018-12-31**
        - 在 [ **開始** ] 功能表中輸入 "regedit"，以尋找並開啟 **登錄編輯程式** 應用程式。
        - 在 [登錄編輯程式] 中，流覽至路徑： **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - 使用與您的 DSN 相同的名稱建立新的子機碼，例如「Contoso 帳戶 ODBC DSN」。
        - 流覽至 [Contoso 帳戶 ODBC DSN] 子機碼。
        - 按一下滑鼠右鍵以加入新的 **字串** 值：
            - 值名稱： **IgnoreSessionToken**
            - 值資料： **1** 個 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="登錄編輯程式設定":::
    - **查詢一致性** ︰選取適用於您的作業的 [一致性層級](consistency-levels.md)。 預設為 [工作階段]。
    - **重試次數** ：輸入初始要求因為服務速率限制而未完成時，重試作業的次數。
    - **結構描述檔案** ：您在這裡有一些選項。
        - 依預設，將此專案保留為 (空白) ，驅動程式會掃描所有容器的第一頁數據，以判斷每個容器的架構。 這就是所謂的容器對應。 如果沒有定義結構描述檔案，驅動程式就必須掃描每一個驅動程式工作階段，而可能導致使用 DSN 的應用程式啟動時間過長。 建議您一律將結構描述檔案與 DSN 產生關連。
        - 如果您已經有架構檔案 (您使用 [架構編輯器]) 建立的架構檔案，您可以按一下 **[流覽]** ，流覽至您的檔案，按一下 [ **儲存** ]，然後按一下 **[確定]** 。
        - 如果您想要建立新的結構描述，請按一下 [確定]，然後按一下主視窗中的 [結構描述編輯器]。 然後繼續處理 [結構描述編輯器] 資訊。 建立新的結構描述檔案之後，請記得返回 [進階選項] 視窗來納入新建立的結構描述檔案。

1. 一旦您完成 [Azure Cosmos DB ODBC 驅動程式 DSN 設定] 視窗並關閉，新的使用者 DSN 會加入到 [使用者 DSN] 索引標籤。

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="[使用者 DSN] 索引標籤上的新 Azure Cosmos DB ODBC DSN":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>步驟3：使用容器對應方法建立架構定義

有兩種類型的取樣方法可供您使用： **容器對應** 或 **資料表分隔符號** 。 取樣會話可以利用這兩種取樣方法，但每個容器只能使用特定的取樣方法。 下列步驟會使用容器對應方法，在一個或多個容器中建立資料的架構。 此取樣方法會抓取容器頁面中的資料，以判斷資料的結構。 它會將容器轉型為 ODBC 端上的資料表。 當容器中的資料是同質時，此取樣方法會有效率且快速。 如果容器包含異質資料類型，建議您使用 [資料表分隔符號對應方法](#table-mapping) ，因為它提供更健全的取樣方法來判斷容器中的資料結構。 

1. 完成 [[連線到您的 Azure Cosmos 資料庫]](#connect)中的步驟1-4 之後，請按一下 **Azure Cosmos DB ODBC 驅動程式 DSN 設定** ] 視窗中的 [ **架構編輯器** ]。

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="[Azure Cosmos DB ODBC 驅動程式 DSN 設定] 視窗中的 [結構描述編輯器] 按鈕":::
1. 在 [結構描述編輯器] 視窗中，按一下 [新建]。
    [ **產生架構** ] 視窗會顯示 Azure Cosmos DB 帳戶中的所有容器。 

1. 選取一或多個要取樣的容器，然後按一下 [ **範例** ]。 

1. 在 [設計檢視] 索引標籤中，會顯示資料庫、結構描述和資料表。 在資料表檢視中，掃描會顯示與資料行名稱 (SQL 名稱、來源名稱等) 相關聯的屬性集。
    針對每個資料行，您可以修改資料行「SQL 名稱」、「SQL 類型」、「SQL 長度」(如果適用的話)、「小數位數」(如果適用的話)、「精確度」(如果適用的話)，以及「可為 Null」。
    - 如果您想要將該資料行從查詢結果中排除，您可以將 [隱藏資料行] 設為 **true** 。 雖然 [隱藏資料行] 標註為 true 的資料行仍屬於結構描述的一部份，它並不會針對選取和投影傳回。 例如，您可以隱藏開頭為 "_" 的所有 Azure Cosmos DB 系統必要屬性。
    - [識別碼] 資料行是唯一無法隱藏的欄位，因為它會在標準化結構描述中作為主索引鍵使用。 

1. 完成架構的定義之後， **請按一下 [**  |  **儲存** 盤案]，流覽至儲存架構的目錄，然後按一下 [ **儲存** ]。

1. 若要使用此架構搭配 DSN，請開啟 [ **AZURE COSMOS DB Odbc 驅動程式 DSN 設定] 視窗** (透過 [Odbc 資料來源管理員]) ，按一下 [ **Advanced Options** ]，然後在 [ **架構** 檔案] 方塊中，流覽至已儲存的架構。 將結構描述檔案儲存至現有的 DSN，會修改 DSN 連線，將範圍設定為由結構描述定義的資料和結構。

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>步驟 4︰使用資料表分隔符號對應方法建立結構描述定義

有兩種類型的取樣方法可供您使用： **容器對應** 或 **資料表分隔符號** 。 取樣會話可以利用這兩種取樣方法，但每個容器只能使用特定的取樣方法。 

下列步驟會使用 **資料表分隔符號** 對應方法，在一個或多個容器中建立資料的架構。 當您的容器包含異質資料類型時，建議您使用此取樣方法。 您可以使用這個方法將取樣範圍設定為一組屬性和其對應值。 比方說，如果文件包含「類型」屬性，您可以將取樣範圍設定為此屬性的值。 取樣的最終結果會是一組針對每一個您所指定之類型值的資料表。 例如，類型 = Car 會產生「汽車」資料表，而類型 = Plane 會產生「飛機」資料表。

1. 完成 [[連線到您的 Azure Cosmos 資料庫]](#connect)中的步驟1-4 之後，請按一下 Azure Cosmos DB ODBC 驅動程式 DSN 設定] 視窗中的 [ **架構編輯器** ]。

1. 在 [結構描述編輯器] 視窗中，按一下 [新建]。
    [ **產生架構** ] 視窗會顯示 Azure Cosmos DB 帳戶中的所有容器。 

1. 在 [ **範例視圖** ] 索引標籤上選取容器，在容器的 [ **對應定義** ] 資料行中，按一下 [ **編輯** ]。 接著在 [對應定義] 視窗中，選取 [資料表分隔符號] 方法。 然後執行以下動作：

    a. 在 [屬性] 方塊中，輸入分隔符號屬性的名稱。 這是您在文件中希望設定取樣範圍的屬性 (例如「City」)，然後按一下 Enter 鍵。 

    b. 如果您只想將取樣範圍設定為您上方輸入的特定屬性值，請在選取方塊中選取該屬性，在 [值] 方塊中輸入值 (例如「Seattle」)，然後按一下 Enter 鍵。 您可以繼續加入多個屬性的值。 只要確定您輸入值時，已選取正確的屬性。

    例如，如果您包含「City」的 **屬性** 值，而您想要限制資料表只包含含有「New York」和「Dubai」之城市值的資料列，可在 [屬性] 方塊中輸入「City」，而在 [值] 方塊中輸入「New York」和「Dubai」。

1. 按一下 [確定]  。 

1. 完成您想要取樣之容器的對應定義之後，請在 [ **架構編輯器** ] 視窗中，按一下 [ **範例** ]。
     針對每個資料行，您可以修改資料行「SQL 名稱」、「SQL 類型」、「SQL 長度」(如果適用的話)、「小數位數」(如果適用的話)、「精確度」(如果適用的話)，以及「可為 Null」。
    - 如果您想要將該資料行從查詢結果中排除，您可以將 [隱藏資料行] 設為 **true** 。 雖然 [隱藏資料行] 標註為 true 的資料行仍屬於結構描述的一部份，它並不會針對選取和投影傳回。 例如，您可以隱藏開頭為 `_` 的所有 Azure Cosmos DB 系統必要屬性。
    - [識別碼] 資料行是唯一無法隱藏的欄位，因為它會在標準化結構描述中作為主索引鍵使用。 

1. 完成架構的定義之後， **請按一下 [**  |  **儲存** 盤案]，流覽至儲存架構的目錄，然後按一下 [ **儲存** ]。

1. 返回 [Azure Cosmos DB ODBC 驅動程式 DSN 設定] 視窗，按一下 [進階選項]。 然後，在 [結構描述檔案] 方塊中，瀏覽至已儲存的結構描述檔案並按一下 [確定]。 再按一下 [確定] 以儲存 DSN。 這會將您建立的結構描述儲存到 DSN。 

## <a name="optional-set-up-linked-server-connection"></a>(選擇性) 設定連結的伺服器連線

您可以藉由設定連結的伺服器連線，從 SQL Server Management Studio (SSMS) 查詢 Azure Cosmos DB。

1. 如[步驟 2](#connect) 所述，建立系統資料來源，例如命名為 `SDS Name`。

1. [安裝 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 並連線至伺服器。 

1. 在 SSMS 查詢編輯器中，使用下列命令為資料來源建立連結的伺服器物件 `DEMOCOSMOS`。 將 `DEMOCOSMOS` 取代成所連結伺服器的名稱，並將 `SDS Name` 取代成系統資料來源的名稱。

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
若要查看新的已連結伺服器名稱，請重新整理 [連結的伺服器] 清單。

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="SSMS 中連結的伺服器":::

### <a name="query-linked-database"></a>查詢連結的資料庫

若要查詢連結的資料庫，請輸入 SSMS 查詢。 在此範例中，查詢會從名為的容器中的資料表中選取 `customers` ：

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

執行查詢。 結果應該與以下類似：

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> 連結的 Cosmos DB 伺服器不支援四部分命名方式。 系統會傳回的類似下列訊息的錯誤：

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(選擇性) 建立檢視
您可以將檢視定義並建立為取樣程序的一部分。 這些檢視相當於 SQL 檢視。 它們都是唯讀，且以定義的 Azure Cosmos DB SQL 查詢的選取項目與投影為範圍。 

若要建立資料的視圖，請在 [ **架構編輯器** ] 視窗的 [ **視圖定義** ] 資料行中，按一下要取樣的容器資料列上的 [ **加入** ]。 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="建立檢視模型":::


接著在 [檢視定義] 視窗中，執行下列動作︰

1. 按一下 [新增]，輸入檢視的名稱 (例如 EmployeesfromSeattleView)，然後按一下 [確定]。

1. 在 [編輯檢視] 視窗中，輸入 Azure Cosmos DB 查詢。 這必須是 [Azure Cosmos DB SQL 查詢](./sql-query-getting-started.md) (例如 `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`)，然後按一下 [確定]。

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="建立檢視時，請加入查詢":::


您可以依需求建立多個檢視。 在您完成定義檢視後，您可以接著取樣資料。 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>步驟 5：在 BI 工具 (例如 Power BI Desktop) 中檢視資料

您可以使用您新的 DSN 將 Azure Cosmos DB 與任何符合 ODBC 規範的工具連線 - 此步驟只會顯示如何連線至 Power BI Desktop 並建立 Power BI 視覺效果。

1. 開啟 Power BI Desktop。

1. 按一下 [取得資料]。

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="取得 Power BI Desktop 中的資料":::

1. 在 [取得資料] 視窗中，按一下 [其他] |  [ODBC] |  [連線]。

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="在 Power BI Get Data 中選擇 ODBC 資料來源":::

1. 在 [從 ODBC] 視窗中，選取您建立的資料來源名稱，然後按一下 [確定]。 您可以將 [進階選項] 項目保留空白。

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="在 Power BI Get Data 中選擇資料來源名稱 (DSN)":::

1. 在 [使用 ODBC 驅動程式存取資料來源] 視窗中，選取 [預設或自訂]，然後按一下 [連線]。 您不需要包含「認證連接字串屬性」。

1. 在 [導覽器] 視窗的左窗格中，展開資料庫，展開結構描述，然後選取資料表。 結果窗格會包含使用您所建立之結構描述的資料。

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="在 Power BI Get Data 中選取資料表":::

1. 若要視覺化 Power BI Desktop 中的資料，請選取資料表名稱前的方塊，然後按一下 [載入]。

1. 在 Power BI Desktop 中，選取最左邊的 [資料] 索引標籤， :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: 確認您的資料已匯入。 

1. 您現在可以按一下 [報表] 索引標籤 :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: 、按一下 [ **新增視覺效果** ]，然後自訂您的磚，以使用 Power BI 建立視覺效果。 如需有關在 Power BI Desktop 中建立視覺效果的詳細資訊，請參閱 [Power BI 中的視覺效果類型](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)。 

## <a name="troubleshooting"></a>疑難排解

如果您收到下列錯誤，請確定您在[步驟 2](#connect) 於 Azure 入口網站複製的「主機」與「存取金鑰」值正確，然後再試一次。 使用 Azure 入口網站中「主機」與「存取金鑰」值右側的 [複製] 按鈕來複製正確的值。

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB，請參閱[歡迎使用 Azure Cosmos DB](introduction.md)。