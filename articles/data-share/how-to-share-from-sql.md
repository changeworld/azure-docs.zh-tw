---
title: 共用和接收來自 Azure SQL Database 和 Azure Synapse Analytics 的資料
description: 瞭解如何從 Azure SQL Database 和 Azure Synapse Analytics 共用及接收資料
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: c13b71858915ab262ab3e0e99ab8c482d19160ea
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318504"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>共用和接收來自 Azure SQL Database 和 Azure Synapse Analytics 的資料

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share 支援以快照集為基礎的共用 Azure SQL Database 和先前的 Azure SQL DW) Azure Synapse Analytics (。 本文說明如何共用和接收來自這些來源的資料。

Azure Data Share 支援從 Azure SQL Database 和 Azure Synapse Analytics (先前的 Azure SQL DW) 共用資料表或觀點。 資料取用者可以選擇接受資料 Azure Data Lake Storage Gen2 或 Azure Blob 儲存體 csv 或 parquet 檔案，以及 Azure SQL Database 和 Azure Synapse Analytics 資料表。

將資料接受 Azure Data Lake 存放區 Gen2 或 Azure Blob 儲存體時，完整快照集會覆寫目標檔案的內容（如果已經存在的話）。
當資料收到資料表，且目標資料表不存在時，Azure Data Share 會使用來源架構建立 SQL 資料表。 如果目標資料表已經存在且具有相同名稱，則會將它卸載並以最新的完整快照集加以覆寫。 目前不支援增量快照集。

## <a name="share-data"></a>共用資料

### <a name="prerequisites-to-share-data"></a>共用資料的必要條件

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 您收件者的 Azure 登入電子郵件地址 (不能使用其電子郵件別名)。
* 如果來源 Azure 資料存放區位於與您將用來建立資料共用資源的不同 Azure 訂用帳戶中，請在 Azure 資料存放區所在的訂用帳戶中註冊 [Microsoft.DataShare 資源提供者](concepts-roles-permissions.md#resource-provider-registration)。 

### <a name="prerequisites-for-sql-source"></a>SQL 來源的必要條件
以下是從 SQL 來源共用資料的必要條件清單。 您也可以遵循 [逐步示範](https://youtu.be/hIE-TjJD8Dc) 來設定必要條件。

* Azure SQL Database 或 Azure Synapse Analytics (先前為 SQL 資料倉儲)，具有您要共用的資料表和檢視。
* 對 SQL Server 上的資料庫進行寫入的權限，存在於 Microsoft.Sql/servers/databases/write  中。 此權限存在於參與者角色中。
* 存取資料倉儲的資料共用權限。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，流覽至 SQL server，並將您自己設定為 Azure Active Directory 系統管理員。
    1. 使用 [查詢編輯器](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 或 SQL Server Management Studio 搭配 Azure Active Directory 驗證連接到 Azure SQL Database/資料倉儲。 
    1. 執行下列腳本，將 Data Share 資源受控識別新增為 db_datareader。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。  

* 具有 'db_datareader' 存取權的 Azure SQL Database 使用者，可瀏覽並選取您想共用的資料表和/或檢視。 

* SQL Server 防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站的 SQL Server 中，瀏覽至 [防火牆和虛擬網路] 
    1. 按一下 **[是]** ， *允許 Azure 服務和資源存取此伺服器* 。
    1. 按一下 [ **+ 新增用戶端 IP** ]。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。
    1. 按一下 [檔案]  。 

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="create-a-data-share-account"></a>建立 Data Share 帳戶

在 Azure 資源群組中建立 Azure Data Share 資源。

1. 選取入口網站左上角的功能表按鈕，再選取 [建立資源] (+)。

1. 搜尋「Data Share」  。

1. 選取 [Data Share]，然後選取 [建立]  。

1. 使用下列資訊填寫 Azure Data Share 資源的基本詳細資料。 

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於資料共用帳戶的 Azure 訂用帳戶。|
    | 資源群組 | *test-resource-group* | 使用現有資源群組，或建立新的資源群組。 |
    | Location | *美國東部 2* | 選取資料共用帳戶的區域。
    | 名稱 | *datashareaccount* | 指定資料共用帳戶的名稱。 |
    | | |

1. 選取 [檢閱 + 建立]，然後選取 [建立] 來佈建您的資料共用帳戶。 佈建新的資料共用帳戶一般需要大約不到 2 分鐘的時間。 

1. 部署完成後，請選取 [移至資源]  。

### <a name="create-a-share"></a>建立共用

1. 瀏覽至 [Data Share 概觀] 頁面。

    ![共用您的資料](./media/share-receive-data.png "共用您的資料") 

1. 選取 [開始共用資料]。

1. 選取 [建立]  。   

1. 填寫共用的詳細資料。 指定名稱、共用類型、共用內容的說明和使用規定 (選擇性)。 

    ![EnterShareDetails](./media/enter-share-details.png "輸入共用詳細資料") 

1. 選取 [繼續]。

1. 若要將資料集新增至共用，請選取 [新增資料集]。 

    ![將資料集新增至共用](./media/datasets.png "資料集")

1. 選取您想要新增的資料集類型。 根據您在先前的步驟中選取的共用類型 (快照集或就地)，您將看到不同的資料集類型清單。 

    ![AddDatasets](./media/add-datasets.png "新增資料集")    

1. 選取您的 SQL server、提供認證，然後選取 **[下一步]** 以流覽至您想要共用的物件，然後選取 [新增資料集]。 

    ![SelectDatasets](./media/select-datasets-sql.png "選取資料集")    

1. 在 [收件者] 索引標籤中，藉由選取 [+ 新增收件者] 來輸入資料取用者的電子郵件地址。 

    ![AddRecipients](./media/add-recipient.png "新增收件者") 

1. 選取 [繼續]。

1. 如果您已選取快照集共用類型，則可以設定快照集排程，將資料的更新提供給資料取用者。 

    ![EnableSnapshots](./media/enable-snapshots.png "啟用快照集") 

1. 選取開始時間和週期間隔。 

1. 選取 [繼續]。

1. 在 [檢閱 + 建立] 索引標籤中，檢閱 [套件內容]、[設定]、[收件者] 及 [同步處理設定]。 選取 [建立]  。

Azure Data Share 現已建立完成，而且 Data Share 的收件者現已準備好接受邀請。 

## <a name="receive-data"></a>接收資料

### <a name="prerequisites-to-receive-data"></a>接收資料的必要條件
您必須先佈建一些 Azure 資源 (下面會列出) 才可以接受資料共用邀請。 

請先確定您已完成所有必要條件再接受資料共用邀請。 

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 資料共用邀請：Microsoft Azure 所送來的邀請，主旨標題為「來自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀請」。
* 在您將建立資料共用資源的 Azure 訂用帳戶及目標 Azure 資料存放區所在的 Azure 訂用帳戶中，註冊 [Microsoft.DataShare 資源提供者](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="prerequisites-for-target-storage-account"></a>目標儲存體帳戶的必要條件
如果您選擇接收資料到 Azure 儲存體，以下是必要條件清單。

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)。 
* 寫入儲存體帳戶的權限，存在於 Microsoft.Storage/storageAccounts/write  中。 此權限存在於參與者角色中。 
* 將角色指派新增至儲存體帳戶的權限，存在於 Microsoft.Authorization/role assignments/write  中。 此權限存在於擁有者角色中。  

### <a name="prerequisites-for-sql-target"></a>SQL 目標的必要條件
如果您選擇將資料接收到 Azure SQL Database 中，Azure Synapse Analytics 下列是必要條件清單。 您也可以遵循 [逐步示範](https://youtu.be/aeGISgK1xro) 來設定必要條件。

* 對 SQL Server 上的資料庫進行寫入的權限，存在於 Microsoft.Sql/servers/databases/write  中。 此權限存在於參與者角色中。 
* 資料共用資源的受控識別用來存取 Azure SQL Database 或 Azure Synapse Analytics 的權限。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站中，流覽至 SQL server，並將您自己設定為 Azure Active Directory 系統管理員。
    1. 使用 [查詢編輯器](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 或 SQL Server Management Studio 搭配 Azure Active Directory 驗證連接到 Azure SQL Database/資料倉儲。 
    1. 執行下列腳本，將 Data Share 受控識別新增為 ' db_datareader、db_datawriter db_ddladmin '。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        請注意， *<share_acc_name>* 是您 Data Share 資源的名稱。 如果您尚未建立 Data Share 資源，您可以稍後再回到此先決條件。         

* SQL Server 防火牆存取。 這可以透過下列步驟完成： 
    1. 在 Azure 入口網站的 SQL Server 中，瀏覽至 [防火牆和虛擬網路] 
    1. 按一下 **[是]** ， *允許 Azure 服務和資源存取此伺服器* 。
    1. 按一下 [ **+ 新增用戶端 IP** ]。 用戶端 IP 位址可能會有所變更。 下次從 Azure 入口網站共用 SQL 資料時，可能需要重複執行此程序。 您也可以新增 IP 範圍。
    1. 按一下 [檔案]  。 

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="open-invitation"></a>開啟邀請

1. 您可以從電子郵件或直接從 Azure 入口網站開啟邀請。 

   若要從電子郵件開啟邀請，請檢查您的收件匣是否有來自您資料提供者的邀請。 邀請會來自 Microsoft Azure，標題為 **來自 <yourdataprovider@domain.com> 的 Azure Data Share 邀請** 。 按一下 [檢視邀請]  以查看您在 Azure 中的邀請。 

   若要直接從 Azure 入口網站開啟邀請，請在 Azure 入口網站中搜尋 **Data Share 邀請** 。 這會帶您前往 Data Share 邀請的清單。

   ![邀請清單](./media/invitations.png "邀請清單") 

1. 選取您想要檢視的共用。 

### <a name="accept-invitation"></a>接受邀請
1. 請確定您已檢閱過所有欄位，包括 [使用規定]  。 如果您同意使用規定，則必須核取該方塊以表示您同意。 

   ![使用規定](./media/terms-of-use.png "使用規定") 

1. 在 [目標 Data Share 帳戶]  底下，選取要作為 Data Share 部署目的地的訂用帳戶和資源群組。 

   針對 [Data Share 帳戶]  欄位，如果您沒有現有的 Data Share 帳戶，請選取 [新建]  。 否則，請選取您想要用來接受資料共用的現有 Data Share 帳戶。 

   針對 [已收到的共用名稱]  欄位，您可以保留資料提供者所指定的預設值，也可以為已接收的共用指定新名稱。 

   同意使用規定並指定了管理收到之共用的資料共用帳戶後，請選取 [接受並設定]。 此時會建立共用訂用帳戶。 

   ![接受選項](./media/accept-options.png "接受選項") 

   同時會帶您前往資料共用帳戶中所接收的共用。 

   如果您不想要接受邀請，請選取 [拒絕]。 

### <a name="configure-received-share"></a>設定已接收的共用
請遵循下列步驟來設定要接收資料的位置。

1. 選取 **資料集** 索引標籤。勾選要指派目的地之資料集旁的方塊。 選取 [+ 對應至目標] 以選擇目標資料存放區。 

   ![對應到目標](./media/dataset-map-target.png "對應到目標") 

1. 選取您希望資料進入的目標資料存放區。 系統將會覆寫目標資料存放區中，具有相同路徑和名稱的任何資料檔案或資料表。 

   ![目標儲存體帳戶](./media/dataset-map-target-sql.png "目標資料存放區") 

1. 對於以快照集為基礎的共用，如果資料提供者已建立快照集排程來定期更新資料，也可以選取 [快照集排程] 索引標籤來啟用快照集排程。勾選快照集排程旁的方塊，然後選取 [+ 啟用]。

   ![啟用快照集排程](./media/enable-snapshot-schedule.png "啟用快照集排程")

### <a name="trigger-a-snapshot"></a>觸發快照集
這些步驟僅適用於以快照集為基礎的共用。

1. 您可以選取 [觸發程序快照集]及 [詳細資料] 索引標籤以觸發快照集。 在這裡，您可以觸發資料的完整或累加快照集。 如果您是第一次接收來自資料提供者的資料，請選取 [完整複本]。 針對 SQL 來源，只支援完整的快照集。

   ![觸發快照集](./media/trigger-snapshot.png "觸發快照集") 

1. 當最後一次執行的狀態為「成功」時，請移至目標資料存放區檢視已接收的資料。 選取 [資料集]，然後按一下 [目標路徑] 中的連結。 

   ![取用者資料集](./media/consumer-datasets.png "取用者資料集對應") 

### <a name="view-history"></a>檢視歷程記錄
此步驟僅適用於以快照集為基礎的共用。 若要檢視快照集的記錄，請選取 [歷程記錄] 索引標籤。您會在這裡找到過去 30 天所有產生的快照集歷程記錄。 

## <a name="supported-data-types"></a>支援的資料類型
當您從 SQL 來源共用資料時，會在快照集處理期間，從 SQL Server 資料類型到 Azure Data Share 過渡期資料類型使用下列對應。 

| SQL Server 資料類型 | Azure Data Share 過渡期資料類型 |
|:--- |:--- |
| BIGINT |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object |
| 文字 |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 1. 針對對應至 Decimal 過渡類型的資料類型，目前快照集最多可支援最多28的精確度。 如果您的資料需要的精確度大於28，請考慮轉換成字串。 
> 1.  如果您要將資料從 Azure SQL database 共用到 Azure Synapse Analytics，則不支援所有資料類型。 如需詳細資料，請參閱 [專用 SQL 集區中的資料表資料類型](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) 。 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted 或動態資料遮罩
目前，Azure Data Share 不支援已設定 Always Encrypted 的 Azure SQL 資料庫。 

針對具有動態資料遮罩的來源 SQL 資料表，資料會顯示在收件者端的遮罩。

## <a name="sql-snapshot-performance"></a>SQL 快照集效能
SQL 快照集效能受到許多因素所影響。 一律建議進行您自己的效能測試。 以下是影響效能的一些範例因素。

* 硬體設定 (例如，來源與目標 SQL 資料存放區的虛擬核心、記憶體、DWU) 。 
* 平行存取來源和目標資料存放區。 如果您要從相同的 SQL 資料存放區共用多個資料表和觀點，或將多個資料表和資料列接收到相同的 SQL 資料存放區，則會影響效能。   
* 來源和目標資料存放區的位置。 

## <a name="troubleshoot-sql-snapshot-failure"></a>針對 SQL 快照集失敗進行疑難排解
快照集失敗最常見的原因是 Data Share 沒有來源或目標資料存放區的許可權。 為了將 Data Share 許可權授與來源或目標 SQL 資料存放區，您必須在使用 Azure Active Directory 驗證連接到 SQL database 時，執行所提供的 SQL 腳本。 若要針對其他 SQL 快照集失敗進行疑難排解，請參閱 [疑難排解快照集失敗](data-share-troubleshoot.md#snapshot-failed)。

## <a name="next-steps"></a>後續步驟
您已瞭解如何使用 Azure Data Share 服務，從 SQL 來源共用及接收資料。 若要深入瞭解如何從其他資料來源共用，請繼續進行 [支援的資料存放區](supported-data-stores.md)。