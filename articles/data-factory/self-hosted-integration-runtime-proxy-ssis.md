---
title: 設定自我裝載整合執行時間作為 SSIS 的 proxy
description: 瞭解如何將自我裝載整合執行時間設定為 Azure SSIS Integration Runtime 的 proxy。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187673"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>在 Azure Data Factory 中，將自我裝載 IR 設定為 Azure SSIS IR 的 proxy

本文說明如何在使用設定為 proxy 的自我裝載整合執行時間（自我裝載 IR）的 Azure Data Factory 中，于 Azure SSIS Integration Runtime （Azure SSIS IR）上執行 SQL Server Integration Services （SSIS）套件。 

透過這項功能，您可以在內部部署存取資料，而不需要將[您的 AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 當您的公司網路設定太複雜或原則過於嚴格，而無法將您的 Azure SSIS IR 插入其中時，此功能會很有用。

這項功能會將包含具有內部部署資料來源之「資料流程」工作的封裝，分割成兩個預備工作： 
* 第一個在自我裝載 IR 上執行的工作，會先將內部部署資料來源的資料移至 Azure Blob 儲存體中的臨時區域。
* 第二個工作是在您的 Azure SSIS IR 上執行，然後將資料從暫存區域移至預期的資料目的地。

這項功能的其他優點和功能，可讓您在 Azure SSIS IR 尚未支援的區域中設定自我裝載 IR，並允許您在資料來源的防火牆上自我裝載 IR 的公用靜態 IP 位址。

## <a name="prepare-the-self-hosted-ir"></a>準備自我裝載 IR

若要使用這項功能，您必須先建立資料處理站，並在其中設定 Azure SSIS IR。 如果您尚未這麼做，請依照[設定 AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)中的指示進行。

接著，您會在已設定 Azure SSIS IR 的相同 data factory 中設定自我裝載 IR。 若要這麼做，請參閱[建立自我裝載 IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。

最後，您可以在內部部署電腦或 Azure 虛擬機器（VM）上下載並安裝最新版的自我裝載 IR，以及額外的驅動程式和執行時間，如下所示：
- 下載並安裝最新版的自我裝載[IR](https://www.microsoft.com/download/details.aspx?id=39717)。
- 如果您在封裝中使用物件連結和內嵌資料庫（OLEDB）連接器，請在已安裝自我裝載 IR 的同一部電腦上下載並安裝相關的 OLEDB 驅動程式（如果您尚未這麼做）。  

  如果您使用較舊版本的 OLEDB 驅動程式進行 SQL Server （SQL Server Native Client [SQLNCLI]），請[下載64位版本](https://www.microsoft.com/download/details.aspx?id=50402)。  

  如果您使用 SQL Server 的 OLEDB 驅動程式（內含 MSOLEDBSQL.H）的最新版本，請[下載64位版本](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  如果您針對其他資料庫系統（例如于 postgresql、MySQL、Oracle 等等）使用 OLEDB 驅動程式，您可以從他們的網站下載64位版本。
- 如果您尚未這麼做，請在已安裝自我裝載 IR 的同一部電腦上，[下載並安裝64位版本的 Visual C++ （VC）運行](https://www.microsoft.com/download/details.aspx?id=40784)時間。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>準備 Azure Blob 儲存體連結服務以進行預備

如果您尚未這麼做，請在您的 Azure SSIS IR 設定所在的相同 data factory 中建立 Azure Blob 儲存體連結的服務。 若要這麼做，請參閱[建立 Azure data factory-連結服務](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)。 請務必執行下列動作：
- 針對 [**資料存放區**]，選取 [ **Azure Blob 儲存體**]。  
- 針對 **[透過整合執行時間連接]** ，選取 [ **AutoResolveIntegrationRuntime**]。  
- 針對 [**驗證方法**]，選取 [**帳戶金鑰**]、[ **SAS URI**] 或 [**服務主體**]。  

    >[!TIP]
    >如果您選取 [ **服務主體**]，請至少授與 *儲存體 Blob 資料參與者* 角色。 如需詳細資訊，請參閱 [Azure Blob 儲存體連接器](connector-azure-blob-storage.md#linked-service-properties)。

![準備 Azure Blob 儲存體連結服務以進行預備](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>使用自我裝載 IR 作為 proxy 來設定 Azure SSIS IR

備妥自我裝載 IR 和 Azure Blob 儲存體連結服務以進行預備，您現在可以使用自我裝載 IR，將新的或現有的 Azure SSIS IR 設定為 data factory 入口網站或應用程式中的 proxy。 不過，在您這麼做之前，如果您現有的 Azure SSIS IR 已在執行，請將它停止，然後重新開機它。

1. 在 [ **Integration runtime 設定**] 窗格中，選取 [**下一步]** ，略過 [**一般設定**] 和 [ **SQL 設定**] 區段。 

1. 在 [ **Advanced settings** ] （設定）區段中，執行下列動作：

   1. 選取 [**設定自我裝載的 Integration Runtime 做為您的 AZURE SSIS Integration Runtime 的 proxy** ] 核取方塊。 

   1. 在 [**自我裝載 Integration Runtime** ] 下拉式清單中，選取您現有的自我裝載 Ir 作為 AZURE SSIS IR 的 proxy。

   1. 在 [**暫存儲存體] [連結服務**] 下拉式清單中，選取您現有的 Azure Blob 儲存體連結服務，或建立一個用於預備的新服務。

   1. 在 [**暫存路徑**] 方塊中，指定所選 Azure blob 儲存體帳戶中的 blob 容器，或將其保留空白，以使用預設值來進行預備。

   1. 選取 **[繼續]** 。

   ![使用自我裝載 IR 的 Advanced 設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

您也可以使用 PowerShell，以自我裝載 IR 作為 proxy 來設定新的或現有的 Azure SSIS IR。

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>啟用 SSIS 套件以透過 proxy 連接

藉由使用適用于 Visual Studio 或獨立安裝程式的最新 SSDT 與 SSIS 專案延伸模組，您可以找到已在 OLEDB 或一般檔案連接管理員中新增的 `ConnectByProxy` 屬性。
* [下載適用于 Visual Studio 的 SSIS 專案延伸模組的 SSDT](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [下載獨立安裝程式](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

當您設計包含具有 OLEDB 或一般檔案來源之資料流程工作的新封裝（可讓您存取內部部署資料庫或檔案）時，您可以在相關連線管理員的 [**屬性**] 窗格中，將這個屬性設定為*True* ，藉以啟用此屬性。

![啟用 ConnectByProxy 屬性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

您也可以在執行現有的封裝時啟用這個屬性，而不需要逐一手動變更它們。  有兩個選項：
- **選項 A**：使用要在您的 AZURE SSIS IR 上執行的最新 SSDT，開啟、重建並重新部署包含這些封裝的專案。 接著，您可以將相關連線管理員的屬性設定為*True* ，藉以啟用此屬性。 當他們從 SSMS 執行封裝時，這些連接管理員會出現在 [**執行封裝**] 快顯視窗的 [**連線管理員**] 索引標籤上。

  ![啟用 ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  您也可以啟用屬性，方法是將它設定為*True* ，以便在[執行 SSIS 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)的 [**連線管理員**] 索引標籤上顯示的相關連線管理員中，當它們在 Data Factory 管線中執行封裝。
  
  ![啟用 ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **選項 B：** 重新部署包含要在 SSIS IR 上執行之封裝的專案。 接著，您可以在**執行** **SSMS 的封裝**時，提供屬性路徑、`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，並將它設定為*True* ，以啟用屬性的屬性覆寫。

  ![啟用 ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  您也可以在 Data Factory 管線中[執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)封裝時，提供屬性路徑、`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，並將它設定為*True* ，以啟用屬性的屬性**覆**寫。
  
  ![啟用 ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debug 第一個和第二個預備工作

在自我裝載 IR 上，您可以在*C:\ProgramData\SSISTelemetry*資料夾中找到執行時間記錄，並在*C:\ProgramData\SSISTelemetry\ExecutionLog*資料夾中尋找第一個預備工作的執行記錄。  您可以在 SSISDB 或指定的記錄路徑中尋找第二個預備工作的執行記錄，這取決於您是將封裝儲存在 SSISDB 還是檔案系統、檔案共用或 Azure 檔案儲存體。 您也可以在第二個預備工作的執行記錄中，尋找第一個預備工作的唯一識別碼。 

![第一個預備工作的唯一識別碼](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>在預備工作中使用 Windows 驗證

如果自我裝載 IR 上的預備工作需要 Windows 驗證，請[將您的 SSIS 套件設定為使用相同的 windows 驗證](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 

您的預備工作將會使用自我裝載 IR 服務帳戶（預設為*NT SERVICE\DIAHostService*）來叫用，而您的資料存放區將會使用 Windows 驗證帳戶來存取。 這兩個帳戶都需要將特定安全性原則指派給它們。 在自我裝載的 IR 機器上，移至 **本機安全性原則** > **本機原則** > **使用者權限指派**，然後執行下列動作：

1. 指派進程的 [*調整記憶體配額*]，並將*進程層級的 token 原則取代*為自我裝載的 IR 服務帳戶。 當您使用預設服務帳戶安裝自我裝載 IR 時，應該會自動發生這種情況。 如果您使用不同的服務帳戶，請為其指派相同的原則。

1. 將 [*以服務方式登*入] 原則指派給 Windows 驗證帳戶。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一和第二個預備工作的計費

在自我裝載 IR 上執行的第一個預備工作會分開計費，就像在自我裝載 IR 上執行的任何資料移動活動一樣，都會計費。 這是在[Azure Data Factory 資料管線定價](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)一文中指定。

在您的 Azure SSIS IR 上執行的第二個預備工作不會另外計費，但您的執行中 Azure SSIS IR 會依照[AZURE SSIS ir 定價](https://azure.microsoft.com/pricing/details/data-factory/ssis/)一文中的指定計費。

## <a name="current-limitations"></a>目前的限制

- 目前僅支援具有開放式資料庫連接（ODBC）、OLEDB 或一般檔案連接管理員以及 ODBC、OLEDB 和一般檔案來源或 OLEDB 目的地的資料流程工作。 
- 目前僅支援以*帳戶金鑰*、*共用存取簽章（SAS） URI*或*服務主體*驗證設定的 Azure Blob 儲存體連結服務。

## <a name="next-steps"></a>後續步驟

在您設定自我裝載 IR 作為 Azure SSIS IR 的 proxy 之後，您可以部署並執行封裝，以在 Data Factory 管線中以「執行 SSIS 套件」活動的形式存取內部部署資料。 若要瞭解作法，請參閱[在 Data Factory 管線中執行 ssis 套件作為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
