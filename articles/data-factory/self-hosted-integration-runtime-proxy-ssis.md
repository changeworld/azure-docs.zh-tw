---
title: 將自我裝載整合執行時間設定為 SSIS 的 proxy
description: 瞭解如何將自我裝載整合執行時間設定為 Azure-SSIS Integration Runtime 的 proxy。
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
ms.date: 11/19/2020
ms.openlocfilehash: 82cc58d46061ec7b623d062ab0b0e5a1fdae7ddd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352213"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>將自我裝載 IR 設定為 Azure Data Factory 中 Azure-SSIS IR 的 proxy

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明如何在 (中使用自我裝載整合執行時間 Azure-SSIS IR 設定為 proxy 的自我裝載整合運行 Azure-SSIS Integration Runtime 時間，在) 中執行 SQL Server Integration Services (SSIS) 套件。 

您可以使用這項功能，存取內部部署資料，而不需要將 [您的 Azure-SSIS IR 加入虛擬網路](./join-azure-ssis-integration-runtime-virtual-network.md)。 當您的公司網路設定太複雜，或原則太過嚴格而無法插入 Azure-SSIS IR 時，此功能就很有用。

這項功能會將您的 SSIS 資料流程工作分解成兩個預備工作（如果適用的話）： 
* **內部部署暫存** 工作：此工作會執行您的資料流程元件，以連接到自我裝載 IR 上的內部部署資料存放區。 它會將資料從內部部署資料存放區移到 Azure Blob 儲存體中的暫存區域，反之亦然。
* **雲端預備** 工作：此工作會執行您的資料流程元件，該元件不會連接到您 Azure-SSIS IR 上的內部部署資料存放區。 它會將資料從 Azure Blob 儲存體中的暫存區域移到雲端資料存放區，反之亦然。

如果您的資料流程工作將資料從內部部署移至雲端，則第一個和第二個預備工作將分別為內部部署和雲端預備工作。 如果您的資料流程工作將資料從雲端移至內部部署，則第一個和第二個預備工作會分別是雲端和內部部署預備工作。 如果您的資料流程工作將資料從內部部署移至內部部署，則第一個和第二個預備工作將會是內部部署預備工作。 如果您的資料流程工作將資料從雲端移至雲端，則這項功能不適用。

例如，此功能的其他優點和功能可讓您在 Azure-SSIS IR 尚未支援的區域中設定自我裝載 IR，並允許您的資料來源防火牆上自我裝載 IR 的公用靜態 IP 位址。

## <a name="prepare-the-self-hosted-ir"></a>準備自我裝載 IR

若要使用此功能，您必須先建立資料處理站，並在其中設定 Azure-SSIS IR。 如果您尚未這麼做，請依照 [設定 Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)中的指示進行。

然後，您可以在設定 Azure-SSIS IR 的相同 data factory 中設定自我裝載 IR。 若要這樣做，請參閱 [建立自我裝載 IR](./create-self-hosted-integration-runtime.md)。

最後，您會在內部部署電腦或 Azure 虛擬機器上，下載並安裝最新版的自我裝載 IR 以及額外的驅動程式和執行時間， (VM) ，如下所示：
- 下載並安裝最新版的自我裝載 [IR](https://www.microsoft.com/download/details.aspx?id=39717)。
- 如果您使用物件連結與嵌入資料庫 (OLEDB) 、開啟資料庫連接 (ODBC) ，或在您的封裝中 ADO.NET 連接器，請在安裝自我裝載 IR 的相同電腦上下載並安裝相關的驅動程式（如果您尚未這樣做）。  

  如果您使用舊版 OLEDB 驅動程式進行 SQL Server (SQL Server Native Client [SQLNCLI] ) ，請 [下載64位版本](https://www.microsoft.com/download/details.aspx?id=50402)。  

  如果您使用最新版本的 OLEDB 驅動程式進行 SQL Server (MSOLEDBSQL.H) ，請 [下載64位版本](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  如果您對其他資料庫系統使用 OLEDB/ODBC/ADO.NET 驅動程式（例如于 postgresql、MySQL、Oracle 等），您可以從其網站下載64位版本。
- 如果您尚未這麼做，請在安裝自我裝載 IR 的同一部電腦上， [下載並安裝64位版本的 Visual C++ (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) 。

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>啟用內部部署預備工作的 Windows 驗證

如果您的自我裝載 IR 上的內部部署預備工作需要 Windows 驗證，請 [將 SSIS 套件設定為使用相同的 Windows 驗證](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 

您的內部部署預備工作將會使用自我裝載 IR 服務帳戶來叫用 (*NT SERVICE\DIAHostService* 預設為) ，而您的資料存放區將會使用 Windows 驗證帳戶存取。 這兩個帳戶都需要指派特定安全性原則。 在自我裝載的 IR 機器上，移至 [**本機安全性原則**  >  **本機原則**  >  **使用者權限指派**]，然後執行下列動作：

1. 指派 *進程的調整記憶體配額* ，並將 *進程等級權杖原則取代* 為自我裝載 IR 服務帳戶。 當您使用預設服務帳戶安裝自我裝載 IR 時，應該會自動發生這種情況。 如果沒有，請手動指派這些原則。 如果您使用不同的服務帳戶，請將相同的原則指派給它。

1. 將 [ *以服務方式登* 入] 原則指派給 Windows 驗證帳戶。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>準備用於預備的 Azure Blob 儲存體連結服務

如果您尚未這麼做，請在已設定 Azure-SSIS IR 的相同 data factory 中建立 Azure Blob 儲存體連結的服務。 若要這樣做，請參閱 [建立 Azure data factory 連結的服務](./quickstart-create-data-factory-portal.md#create-a-linked-service)。 請務必執行下列動作：
- 針對 [ **資料存放區**]，選取 [ **Azure Blob 儲存體**]。  
- 針對 **[透過整合運行** 時間連線]，請選取 [ **AutoResolveIntegrationRuntime** (不是您的 Azure-SSIS IR 或自我裝載的 IR) ，因為我們會使用預設 Azure IR 來提取您的 Azure Blob 儲存體的存取認證。
- 針對 [ **驗證方法**]，選取 [ **帳戶金鑰**]、[ **SAS URI**]、[ **服務主體**] 或 [ **受控識別**]。  

>[!TIP]
>如果您選取 **服務主體** 方法，請至少將 *儲存體 Blob 資料參與者* 角色授與服務主體。 如需詳細資訊，請參閱 [Azure Blob 儲存體連接器](connector-azure-blob-storage.md#linked-service-properties)。 如果您選取 [ **受控識別** ] 方法，請將您的 ADF 受控識別授與適當的角色，以存取 Azure Blob 儲存體。 如需詳細資訊，請參閱 [使用 Azure Active Directory 驗證搭配 ADF 受控身分識別存取 Azure Blob 儲存體](/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication)。

![準備 Azure Blob 儲存體連結服務以進行預備](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>使用自我裝載 IR 設定 Azure-SSIS IR 作為 proxy

準備好您的自我裝載 IR 和 Azure Blob 儲存體連結服務以進行預備，您現在可以使用自我裝載 IR 來設定新的或現有的 Azure-SSIS IR，以作為 data factory 入口網站或應用程式中的 proxy。 不過，在您這麼做之前，如果現有的 Azure-SSIS IR 已在執行中，請停止它，然後重新開機它。

1. 在 [**整合執行時間設定**] 窗格中，選取 [**下一步]** 略過 [**一般設定**] 和 [ **SQL 設定**] 區段。 

1. 在 [ **Advanced settings** ] 區段中，執行下列動作：

   1. 選取 [ **將 Self-Hosted Integration Runtime 設定為 Azure-SSIS Integration Runtime 的 proxy** ] 核取方塊。 

   1. 在 [ **自我裝載 Integration Runtime** ] 下拉式清單中，選取您現有的自我裝載 IR 作為 Azure-SSIS IR 的 proxy。

   1. 在 [ **暫存儲存體連結服務** ] 下拉式清單中，選取您現有的 Azure Blob 儲存體連結服務，或建立一個新的服務來進行暫存。

   1. 在 [ **暫存路徑** ] 方塊中，指定您所選 Azure blob 儲存體帳戶中的 blob 容器，或將其保留空白，以使用預設值來進行暫存。

   1. 選取 [繼續]  。

   ![使用自我裝載 IR 的 Advanced 設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

您也可以使用 PowerShell，將新的或現有的 Azure-SSIS IR 設定為使用自我裝載 IR 作為 proxy。

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

藉由使用最新的 SSDT 做為 Visual Studio 或獨立安裝程式的 SSIS 專案擴充功能，您可以 `ConnectByProxy` 在連線管理員中找到已針對支援的資料流程元件新增的新屬性。
* [下載 Visual Studio 的 SSIS 專案擴充功能](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [下載獨立安裝程式](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

當您使用可存取內部部署資料的元件來設計包含資料流程工作的新封裝時，您可以在相關連接管理員的 [**屬性**] 窗格中，將此屬性設定為 [ *True* ]，以啟用此屬性。

![啟用 ConnectByProxy 屬性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

您也可以在執行現有的封裝時啟用這個屬性，而不需要逐一手動變更。  有兩個選項：
- **選項 A**：開啟、重建和重新部署包含這些套件的專案，其中包含要在 Azure-SSIS IR 上執行的最新 SSDT。 然後，您可以針對相關的連接管理員將屬性設為 *True* ，以啟用屬性。 當您從 SSMS 執行封裝時，這些連線管理員會出現在 [**執行封裝**] 快顯視窗的 [**連接管理員**] 索引標籤上。

  ![啟用 ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  您也可以將屬性設為 *True* ，以便在 Data Factory 管線中執行封裝時，在 [[執行 SSIS 封裝活動](./how-to-invoke-ssis-package-ssis-activity.md)] 的 [**連接管理員**] 索引標籤上出現的相關連線管理員上啟用屬性。
  
  ![啟用 ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **選項 B：** 重新部署包含這些套件的專案，以在 SSIS IR 上執行。 然後，您可以藉由提供屬性路徑，並將 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` 它設定為 *True* ，在您從 SSMS 執行封裝時，將它設為 True，以做為 [**執行封裝**] 快顯視窗之 [ **Advanced** ] 索引標籤上的屬性覆寫。

  ![啟用 ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  您也可以藉由提供屬性路徑來啟用屬性， `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` 並在 Data Factory 管線中執行封裝時，將它設定為 *True* 做為 [執行 SSIS 套件活動](./how-to-invoke-ssis-package-ssis-activity.md)之 [**屬性覆** 寫] 索引標籤上的屬性覆寫。
  
  ![啟用 ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>對內部部署和雲端預備工作進行調試

在自我裝載 IR 上，您可以在 *C:\ProgramData\SSISTelemetry* 資料夾中找到執行時間記錄，並在 *C:\ProgramData\SSISTelemetry\ExecutionLog* 資料夾中找到內部部署暫存工作的執行記錄。  您可以在 SSISDB 中尋找雲端預備工作的執行記錄、指定的記錄檔路徑，或根據您是否將套件儲存在 SSISDB 中、啟用 [Azure 監視器整合](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)等等，來 Azure 監視器。您也可以在雲端預備工作的執行記錄中，尋找內部部署預備工作的唯一識別碼。 

![第一個暫存工作的唯一識別碼](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

如果您已提出客戶支援票證，您可以在自我裝載 IR 上安裝的 **Microsoft Integration Runtime Configuration Manager** 的 [**診斷**] 索引標籤上選取 [**傳送記錄** 檔] 按鈕，以傳送最新的作業/執行記錄以供我們調查。

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>內部部署和雲端預備工作的計費

在自我裝載 IR 上執行的內部部署預備工作會分開計費，就像在自我裝載 IR 上執行的任何資料移動活動都會計費。 這是在 [Azure Data Factory 資料管線定價](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 文章中指定的。

在 Azure-SSIS IR 上執行的雲端預備工作不會個別計費，但您執行的 Azure-SSIS IR 會依 [Azure-SSIS IR 定價](https://azure.microsoft.com/pricing/details/data-factory/ssis/) 文章中的指定計費。

## <a name="enable-custom3rd-party-components"></a>啟用自訂/協力廠商元件 

若要讓您的自訂/協力廠商元件使用自我裝載 IR 來存取內部部署資料，以做為 Azure-SSIS IR 的 proxy，請遵循下列指示：

1. 透過 [標準/快速自訂](./how-to-configure-azure-ssis-ir-custom-setup.md)設定，在 Azure-SSIS IR 上安裝以 SQL Server 2017 為目標的自訂/協力廠商元件。

1. 在自我裝載 IR 上建立下列 DTSPath 登錄機碼（如果尚未存在）：
   1. 請將 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` 設為 `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. 請將 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` 設為 `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. 將自訂/協力廠商元件安裝在上述 DTSPath 的自我裝載 IR 上以 SQL Server 2017 為目標，並確定您的安裝程式：

   1. 建立 `<DTSPath>` 、 `<DTSPath>/Connections` 、 `<DTSPath>/PipelineComponents` 和 `<DTSPath>/UpgradeMappings` 資料夾（如果尚未存在）。
   
   1. 針對資料夾中的延伸模組對應建立您自己的 XML 檔 `<DTSPath>/UpgradeMappings` 。
   
   1. 在全域組件快取 (GAC) 中，安裝自訂/協力廠商元件元件所參考的所有元件。

以下是我們的合作夥伴、 [Theobald Software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) 和 [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir)的範例，他們已調整其元件以使用快速自訂安裝和自我裝載 IR 作為 Azure-SSIS IR 的 proxy。

## <a name="enforce-tls-12"></a>強制使用 TLS 1.2

如果您需要使用強式加密/更安全的網路通訊協定 (TLS 1.2) 並在自我裝載 IR 上停用較舊的 SSL/TLS 版本，您可以下載並執行可在公開預覽容器的 *CustomSetupScript/UserScenarios/tls 1.2* 資料夾中找到的 *main .cmd* 腳本。  您可以使用 [Azure 儲存體總管](https://storageexplorer.com/)來輸入下列 SAS URI，以連接到我們的公開預覽容器：

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>目前的限制

- 目前只支援在 Azure-SSIS IR Standard Edition 上內建/預先安裝的資料流程元件（Hadoop/HDFS/DQS 元件除外），請參閱 [Azure-SSIS IR 上的所有內建/預先安裝元件](./built-in-preinstalled-components-ssis-integration-runtime.md)。
- 目前僅支援以 managed 程式碼撰寫的自訂/協力廠商資料流程元件 ( .NET Framework) ，但目前不支援以機器碼撰寫的自訂/協力廠商資料流程元件 (c + +) 。
- 目前不支援在內部部署和雲端暫存工作中變更變數值。
- 在內部部署暫存工作中變更類型 object 的變數值，將不會反映在其他工作中。
- 目前不支援 OLEDB 來源中的 *ParameterMapping* 。 因應措施是，請使用 *來引數的 Sql 命令* 做為 *AccessMode* ，並使用 *運算式* 在 sql 命令中插入您的變數/參數。 如需說明，請參閱 *ParameterMappingSample .dtsx* 套件，此套件可在我們的公開預覽容器的 *SelfHostedIRProxy/限制* 資料夾中找到。 您可以使用 Azure 儲存體總管，輸入上述的 SAS URI，以連接到我們的公開預覽容器。

## <a name="next-steps"></a>後續步驟

在您將自我裝載 IR 設定為 Azure-SSIS IR 的 proxy 之後，您可以部署和執行您的封裝，以存取內部部署資料，作為 Data Factory 管線中的執行 SSIS 套件活動。 若要深入瞭解，請參閱 [在 Data Factory 管線中執行 Ssis 封裝作為執行 Ssis 套件活動](./how-to-invoke-ssis-package-ssis-activity.md)。