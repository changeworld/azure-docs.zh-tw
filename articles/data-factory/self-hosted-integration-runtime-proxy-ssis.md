---
title: 將自託管集成運行時配置為 SSIS 的代理
description: 瞭解如何將自託管集成運行時配置為 Azure-SSIS 集成運行時的代理。
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
ms.date: 03/27/2020
ms.openlocfilehash: 9a1923057bc318869f491791520aacb4d0d17591
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346625"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>將自承載的 IR 配置為 Azure 資料工廠中的 Azure-SSIS IR 的代理

本文介紹如何在 Azure 資料工廠中的 Azure-SSIS 集成運行時 （Azure-SSIS IR） 上運行 SQL 伺服器整合服務 （SSIS） 包，並配置為代理的自託管集成運行時（自託管 IR）。 

借助此功能，您可以在本地訪問資料，而無需[將 Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 當公司網路的配置過於複雜或策略過於嚴格，無法將 Azure-SSIS IR 注入其中時，此功能非常有用。

此功能將具有本地資料來源的任何 SSIS 資料流程任務拆分為兩個暫存任務： 
* 第一個任務在自託管 IR 上運行，首先將資料從本地資料來源移動到 Azure Blob 存儲中的暫存區域。
* 第二個任務在 Azure-SSIS IR 上運行，然後將資料從暫存區域移動到預期資料目標。

例如，此功能的其他優點和功能允許您在 Azure-SSIS IR 尚未支援的地區設定自託管 IR，並允許在資料來源的防火牆上使用自託管 IR 的公共靜態 IP 位址。

## <a name="prepare-the-self-hosted-ir"></a>準備自託管的 IR

要使用此功能，請首先創建資料工廠並在其中設置 Azure-SSIS IR。 如果尚未這樣做，請按照[設置 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)中的說明操作。

然後，在 Azure-SSIS IR 所在的同一資料工廠中設置自託管 IR。 為此，請參閱[創建自託管 IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。

最後，在本地電腦或 Azure 虛擬機器 （VM） 上下載並安裝最新版本的自託管 IR 以及其他驅動程式和運行時，如下所示：
- 下載並安裝[最新版本的自託管 IR](https://www.microsoft.com/download/details.aspx?id=39717)。
- 如果在包中使用物件連結和嵌入資料庫 （OLEDB） 連接器，請在安裝自託管 IR 的同一台電腦上下載並安裝相關的 OLEDB 驅動程式（如果尚未安裝）。  

  如果將早期版本的 OLEDB 驅動程式用於 SQL Server（SQL Server 本機用戶端 [SQLNCLI]），[請下載 64 位版本](https://www.microsoft.com/download/details.aspx?id=50402)。  

  如果您使用最新版本的 OLEDB 驅動程式進行 SQL 伺服器 （MSOLEDBSQL），[請下載 64 位版本](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  如果將 OLEDB 驅動程式用於其他資料庫系統（如 PostgreSQL、MySQL、Oracle 等），則可以從其網站下載 64 位版本。
- 如果尚未執行此操作，[請下載並安裝 64 位版本的 Visual C++ （VC） 運行時](https://www.microsoft.com/download/details.aspx?id=40784)，該主機安裝自託管 IR。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>準備用於暫存的 Azure Blob 存儲連結服務

如果尚未這樣做，請在同一資料工廠中創建 Azure Blob 存儲連結服務，並設置 Azure-SSIS IR。 為此，請參閱創建[Azure 資料工廠連結的服務](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)。 請務必執行以下操作：
- 對於**資料存儲**，選擇**Azure Blob 存儲**。  
- 對於**通過集成運行時進行連接**，請選擇 **"自動解析集成運行時**"（不是 Azure-SSIS IR 或自託管 IR），因為我們使用預設的 Azure IR 來獲取 Azure Blob 存儲的訪問憑據  
- 對於**身份驗證方法**，選擇**帳戶金鑰** **、SAS URI****或服務主體**。  

    >[!TIP]
    >如果選擇**服務主體**方法，請至少授予服務主體存儲 Blob *資料參與者* 角色。 有關詳細資訊，請參閱 Azure [Blob 存儲連接器](connector-azure-blob-storage.md#linked-service-properties)。

![準備用於暫存的 Azure Blob 存儲連結服務](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>使用自託管 IR 配置 Azure-SSIS IR 作為代理

準備好自託管的 IR 和 Azure Blob 存儲連結服務進行暫存後，現在可以將新的或現有的 Azure-SSIS IR 與自託管 IR 配置為數據工廠門戶或應用中的代理。 但是，在執行此操作之前，如果現有的 Azure-SSIS IR 已在運行，請停止它，然後重新開機它。

1. 在 **"集成運行時設置"** 窗格中，通過選擇 **"下一步**"跳過 **"常規設置**"和 **"SQL 設置"** 部分。 

1. 在 **"高級設置"** 部分中，執行以下操作：

   1. 選擇"**設置自託管集成運行時作為 Azure-SSIS 集成運行時的代理**"核取方塊。 

   1. 在**自託管集成運行時**下拉清單中，選擇現有的自託管 IR 作為 Azure-SSIS IR 的代理。

   1. 在**暫存存儲連結服務**下拉清單中，選擇現有的 Azure Blob 存儲連結服務或創建新的暫存服務。

   1. 在 **"暫存"路徑**框中，在選定的 Azure Blob 存儲帳戶中指定 Blob 容器，或將其留空以使用預設容器進行暫存。

   1. 選取 **[繼續]**。

   ![具有自承載 IR 的高級設置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

您還可以使用 PowerShell 將新的或現有的 Azure-SSIS IR 與自託管 IR 配置為代理。

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>啟用 SSIS 包通過代理連接

通過將最新的 SSDT 與 Visual Studio 的 SSIS 專案副檔名或獨立安裝程式使用，`ConnectByProxy`可以找到已在 OLEDB 或一般檔案連線管理員中添加的新屬性。
* [下載 SSDT 與 SSIS 專案擴展為視覺化工作室](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [下載獨立安裝程式](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

當您使用 OLEDB 或 Flat File 源設計包含資料流程任務的新包（允許在本地訪問資料庫或檔）時，可以通過在相關連線管理員的 **"屬性"** 窗格中將其設置為*True*來啟用此屬性。

![啟用 ConnectByProxy 屬性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

在運行現有包時，還可以啟用此屬性，而無需逐個手動更改它們。  有兩個選項：
- **選項 A**： 打開、重新生成和重新部署包含具有最新 SSDT 的包的專案，以在 Azure-SSIS IR 上運行。 然後，您可以通過將屬性設置為 *"為相關連線管理員"為 True"* 來啟用該屬性。 當他們從 SSMS 運行包時，這些連線管理員將顯示在 **"執行包"** 快顯視窗的連接**管理器**選項卡上。

  ![啟用 ConnectByProxy 屬性2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  您還可以通過將在資料工廠管道中運行包時顯示在["執行 SSIS 包活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)**的連線管理員**"選項卡上的相關連線管理員設置為*True*來啟用該屬性。
  
  ![啟用 ConnectByProxy 屬性3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **選項 B：** 重新部署包含這些包的專案，以在 SSIS IR 上運行。 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`然後，您可以在從 SSMS 運行包時，通過提供屬性路徑，並將其設置為*True，* 將其設置為 **"執行包**"快顯視窗的 **"高級**"選項卡上的屬性覆蓋。

  ![啟用 ConnectByProxy 屬性4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  還可以通過提供`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`屬性路徑來啟用該屬性，並在資料工廠管道中運行包時，將其設置為*True*作為屬性覆蓋"執行[SSIS 包活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)**的屬性覆蓋**"選項卡。
  
  ![啟用 ConnectByProxy 屬性5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>調試第一個和第二個暫存任務

在自託管 IR 上，您可以在*C：_程式資料_SSIS遙測*資料夾中找到運行時日誌，並在*C：*程式資料_SSIS遙測_執行日誌*資料夾中找到第一個暫存任務的執行日誌。  您可以在 SSISDB 或指定的日誌記錄路徑中找到第二個暫存任務的執行日誌，具體取決於將包存儲在 SSISDB 或檔案系統、檔共用還是 Azure 檔中。 您還可以在第二個暫存任務的執行日誌中找到第一個暫存任務的唯一 ID。 

![第一個暫存任務的唯一 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>在暫存任務中使用 Windows 身份驗證

如果自託管 IR 上的暫存任務需要 Windows 身份驗證，[請將 SSIS 包配置為使用相同的 Windows 身份驗證](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 

您的暫存任務將使用自託管的 IR 服務帳戶 *（NT SERVICE_DIAHostService，* 預設情況下）調用，並且您的資料存儲將使用 Windows 身份驗證帳戶訪問。 這兩個帳戶都需要為其分配某些安全性原則。 在自託管 IR 電腦上，轉到**本地安全性原則** > **本地策略本地策略** > **使用者許可權分配**，然後執行以下操作：

1. 為*進程分配"調整記憶體配額*"，並將*進程級權杖策略替換為*自託管的 IR 服務帳戶。 當您使用預設服務帳戶安裝自託管的 IR 時，應會自動執行此操作。 如果沒有，請手動分配這些策略。 如果您使用不同的服務帳戶，請為其分配相同的策略。

1. 將*登錄作為服務*策略分配給 Windows 身份驗證帳戶。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一個和第二個暫存任務的計費

在自託管 IR 上運行的第一個暫存任務單獨計費，就像對在自託管 IR 上運行的任何資料移動活動進行計費一樣。 這在[Azure 資料工廠資料管道定價](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)文章中指定。

在 Azure-SSIS IR 上運行的第二個暫存任務不單獨計費，但正在運行的 Azure-SSIS IR 將在[Azure-SSIS IR 定價](https://azure.microsoft.com/pricing/details/data-factory/ssis/)文章中指定計費。

## <a name="enabling-tls-12"></a>啟用 TLS 1.2

如果您需要使用增強式加密/更安全的網路通訊協定 （TLS 1.2） 並在自託管的 IR 上禁用較舊的 SSL/TLS 版本，則可以下載並運行*主.cmd*腳本，該腳本可在我們的公共預覽容器的*CustomSetupScript/UserS/TLS 1.2*資料夾中找到。  使用[Azure 存儲資源管理器](https://storageexplorer.com/)，可以通過輸入以下 SAS URI 連接到我們的公共預覽容器：

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>目前的限制

- 目前僅支援具有開放資料庫連接 （ODBC）/OLEDB/一般檔案源的資料流程任務。 
- 目前僅支援配置帳戶*金鑰*、*共用訪問簽名 （SAS） URI*或*服務主體*身份驗證的 Azure Blob 存儲連結服務。
- 目前不支援在 OLEDB 源中*進行參數映射*。 作為解決方法，請使用 SQL*命令"從變數*"作為*AccessMode，* 並使用*運算式*在 SQL 命令中插入變數/參數。 例如，請參閱可在公共預覽容器的*Self託管IRProxy/限制*資料夾中找到的*參數映射Sample.dtsx*包。 使用 Azure 存儲資源管理器，您可以通過輸入上述 SAS URI 連接到我們的公共預覽容器。

## <a name="next-steps"></a>後續步驟

將自承載的 IR 配置為 Azure-SSIS IR 的代理後，可以部署和運行包以在本地訪問資料，如在資料工廠管道中執行 SSIS 包活動。 要瞭解如何，請參閱[在資料工廠管道中將 SSIS 包作為執行 SSIS 包活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
