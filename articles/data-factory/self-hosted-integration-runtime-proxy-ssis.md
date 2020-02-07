---
title: 設定自我裝載整合執行時間作為 SSIS 的 proxy
description: 瞭解如何設定自我裝載 Integration Runtime 做為 Azure SSIS Integration Runtime 的 proxy。
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
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048959"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>設定自我裝載 IR 作為 ADF 中的 Azure SSIS IR 的 proxy

本文說明如何使用設定為 proxy 的自我裝載 IR，在 Azure Data Factory （ADF）中的 Azure SSIS Integration Runtime （IR）上執行 SQL Server Integration Services （SSIS）套件。  這項功能可讓您存取內部部署資料，而不需要將[您的 AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。  當您的公司網路具有過於複雜的設定/限制原則，讓您在其中插入您的 Azure SSIS IR 時，這會很有用。

這項功能會將包含具有內部部署資料來源之資料流程工作的套件分割成兩個預備工作：在自我裝載 IR 上執行的第一個作業會先將資料從內部部署資料來源移到您 Azure Blob 儲存體的臨時區域中，而在您的 Azure SSIS IR 上執行的第二個，會將資料從暫存區域移至預期的資料目的地。

這項功能也提供其他優點/功能，因為它可讓您在 Azure SSIS IR 尚未支援的區域中布建自我裝載 IR，並允許您在資料來源的防火牆上自我裝載 IR 的公用靜態 IP 位址等等。

## <a name="prepare-self-hosted-ir"></a>準備自我裝載 IR

若要使用這項功能，您必須先依照如何布建[AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)一文中的說明來建立 ADF，並在其下布建您的 AZURE ssis ir （如果尚未這麼做）。

接著，您必須依照[如何建立自我裝載 ir](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)一文，在布建您的 AZURE SSIS ir 的同一個 ADF 下布建自我裝載 ir。

最後，您必須在內部部署機器/Azure 虛擬機器（VM）上下載並安裝最新版的自我裝載 IR，以及額外的驅動程式和執行時間，如下所示：
- 從[這裡](https://www.microsoft.com/download/details.aspx?id=39717)下載並安裝最新版的自我裝載 IR。
- 如果您在封裝中使用 OLEDB 連接器，請在已安裝自我裝載 IR 的同一部電腦上下載並安裝相關的 OLEDB 驅動程式（如果您尚未這麼做）。  如果您使用舊版 OLEDB driver for SQL Server （SQLNCLI），您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=50402)下載64位版本。  如果您使用最新版本的 OLEDB driver for SQL Server （內含 MSOLEDBSQL.H），您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=56730)下載64位版本。  如果您針對其他資料庫系統（例如于 postgresql、MySQL、Oracle 等）使用 OLEDB 驅動程式，您可以從其各自的網站下載64位版本。
- 在已安裝自我C++裝載 IR 的同一部電腦上下載並安裝 Visual （VC）執行時間（如果您尚未這麼做）。  您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=40784)下載64位版本。

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>準備 Azure Blob 儲存體連結服務以進行預備

依照[如何建立 ADF 連結服務一](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)文中的說明，在布建您的 AZURE SSIS IR 的相同 ADF 下建立 Azure Blob 儲存體連結服務。  請確定下列事項：
- 已選取**資料存放區**的**Azure Blob 儲存體**
- 已選取**AutoResolveIntegrationRuntime**以透過**整合執行時間連接**
- **帳戶金鑰**/**SAS URI**/**服務主體**已針對**驗證方法**選取

>[!TIP]
>選取  **服務主體** 時，請至少授與 **儲存體 Blob 資料參與者角色**。 如需詳細資訊，請參閱 [Azure Blob 儲存體連接器](connector-azure-blob-storage.md#linked-service-properties)。

![準備 Azure Blob 儲存體連結服務以進行預備](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>使用自我裝載 IR 作為 proxy 來設定 Azure SSIS IR

準備好您的自我裝載 IR 和 Azure Blob 儲存體連結服務以進行預備，您現在可以使用自我裝載 IR 來設定新的/現有的 Azure SSIS IR，以作為 ADF 入口網站/應用程式上的 proxy。  如果您現有的 Azure SSIS IR 正在執行，請先將它停止，然後再重新開機它。

1. 在 [整合執行時間設定] 面板上，選取 [**下一步]** 按鈕，前進到 [**一般設定**] 和 [ **SQL 設定**] 區段。 

1. 在 [ **Advanced Settings** ] （設定）區段上：

   1. 選取 [**設定自我裝載的 Integration Runtime 做為您的 AZURE SSIS Integration Runtime 的 proxy** ] 核取方塊。 

   1. 對於**自我**裝載的 Integration Runtime，請選取您現有的自我裝載 IR 作為 AZURE SSIS IR 的 proxy。

   1. 針對**暫存儲存體連結服務**，請選取您現有的 Azure Blob 儲存體連結服務，或建立一個新的來進行預備。

   1. 針對 [**暫存路徑**]，請在您選取的 Azure blob 儲存體帳戶中指定 blob 容器，或將其保留空白，以使用預設值來進行預備。

   1. 選取 [繼續]。

   ![使用自我裝載 IR 的 Advanced 設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

您也可以使用 PowerShell，以自我裝載 IR 作為 proxy 來設定新的/現有的 Azure SSIS IR。

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

使用最新的 SSDT 與 SSIS 專案的 Visual Studio 擴充功能，可從[這裡](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下載，或作為可從[這裡](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)下載的獨立安裝程式，您可以找到已在 OLEDB/一般檔案連接管理員中新增的新**ConnectByProxy**屬性。  

當您使用 OLEDB/一般檔案來源來設計包含資料流程工作的新封裝，以存取內部部署資料庫/檔案時，您可以在相關連線管理員的 [屬性] 面板上，將此屬性設定為**True** ，藉以啟用此屬性。

![啟用 ConnectByProxy 屬性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

您也可以在執行現有的封裝時啟用此屬性，而不需要逐一手動變更它們。  有兩個選項：
- 使用要在 Azure SSIS IR 上執行的最新 SSDT 來開啟、重建和重新部署包含這些套件的專案：此屬性可以藉由將其設定為**True** ，以便在從 SSMS 執行封裝時，出現在 [執行封裝] 快顯視窗的 [**連接管理**器] 索引標籤上的相關連線管理員中啟用。

  ![啟用 ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  您也可以將屬性設定為 True，以針對在 ADF 管線中執行封裝時，[[執行 SSIS 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)] 的 [**連線管理員**] 索引標籤上顯示的相關連線管理員，將它設為**True** 。
  
  ![啟用 ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- 重新部署包含要在 SSIS IR 上執行之封裝的專案：您可以藉由提供屬性路徑、`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，並在從 SSMS 執行封裝時，將它設定為**True** ，做為 [執行封裝] 快顯視窗的 [ **Advanced** ] 索引標籤上的屬性覆寫來啟用此屬性。

  ![啟用 ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  在 ADF 管線中[執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)封裝時，也可以藉由提供屬性路徑、`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，並將它設定為**True**做為屬性**覆**寫，來啟用屬性。
  
  ![啟用 ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debug 第一個和第二個預備工作

在自我裝載 IR 上，您可以在 `C:\ProgramData\SSISTelemetry` 資料夾中找到執行時間記錄，並在 `C:\ProgramData\SSISTelemetry\ExecutionLog` 資料夾中尋找第一個暫存工作的執行記錄。  第二個預備工作的執行記錄可以在您的 SSISDB 或指定的記錄路徑中找到，這取決於您是在 SSISDB 或檔案系統/檔案共用/Azure 檔案儲存體中分別儲存封裝。  第一個預備工作的唯一識別碼也可以在第二個預備工作的執行記錄中找到，例如 

![第一個預備工作的唯一識別碼](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>在預備工作中使用 Windows 驗證

如果您在自我裝載 IR 上的預備工作需要 Windows 驗證，您需要[設定 SSIS 套件以使用相同的 windows 驗證](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 您的預備工作將會使用自我裝載 IR 服務帳戶（預設為`NT SERVICE\DIAHostService`）來叫用，而且您的資料存放區將會使用 Windows 驗證帳戶來存取。 這兩個帳戶都需要將特定安全性原則指派給它們。 因此，在自我裝載的 IR 機器上，開啟 `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` 並完成下列步驟。
- 指派進程的 [**調整記憶體配額**]，並將**進程層級的 token 原則取代**為自我裝載的 IR 服務帳戶。 這應該會在使用預設服務帳戶安裝自我裝載 IR 時自動完成。 如果您使用不同的服務帳戶，請為其指派相同的原則。
- 將 [**以服務方式登**入] 原則指派給 Windows 驗證帳戶。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一和第二個預備工作的計費

在自我裝載 IR 上執行的第一項預備工作，將會以與在自我裝載 IR 上執行的任何資料移動活動相同的方式計費，如同[ADF 資料管線定價](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)一文中所指定。

在您的 Azure SSIS IR 上執行的第二個預備工作將不會分開計費，但執行中的 Azure SSIS IR 會依照[AZURE SSIS ir 定價](https://azure.microsoft.com/pricing/details/data-factory/ssis/)一文中的指定計費。

## <a name="current-limitations"></a>目前的限制

- 目前僅支援使用 ODBC/OLEDB/一般檔案連接管理員和 ODBC/OLEDB/一般檔案來源或 OLEDB 目的地的資料流程工作。 
- 目前僅支援 Azure Blob 儲存體使用**帳戶金鑰**/**SAS URI**設定的已連結服務，/**服務主體**驗證。

## <a name="next-steps"></a>後續步驟

將自我裝載 IR 設定為 Azure SSIS IR 的 proxy 之後，您就可以部署並執行封裝，以在 ADF 管線中以「執行 SSIS 套件」活動的形式存取內部部署資料，請參閱[在 adf 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
