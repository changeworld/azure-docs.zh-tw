---
title: 將傳統 Windows VM 計量傳送至 Azure 監視器計量資料庫
description: 將客體 OS 計量傳送到 Windows 虛擬機器的 Azure 監視器資料存放區 (傳統)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655799"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>將客體作業系統計量傳送至 Windows 虛擬機器的 Azure 監視器計量資料庫（傳統）

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure 監視器[診斷擴充功能](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (也稱為 "WAD" 或「診斷」) 可讓您從當作虛擬機器、雲端服務或 Service Fabric 叢集一部分執行的客體作業系統 (客體 OS) 收集計量與記錄。 擴充功能可以將遙測資料傳送到[許多不同位置](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)。

本文說明將 Windows 虛擬機器（傳統）的來賓 OS 效能計量傳送至 Azure 監視器公制資料庫的程式。 從診斷 1.11 版開始，您可以直接將計量寫入到已收集標準平台計量的 Azure 監視器計量存放區。 

將計量儲存在此位置，可讓您存取與您對平台計量執行的相同動作。 動作包括近乎即時的警示、圖表、路由、從 REST API 存取以及更多功能。 在過去，診斷擴充功能會寫入到 Azure 儲存體，而不是 Azure 監視器資料存放區。 

本文所述的流程僅適用於執行 Windows 作業系統的傳統虛擬機器。

## <a name="prerequisites"></a>先決條件

- 您必須是 Azure 訂用帳戶的[服務管理員或共同管理員](../../cost-management-billing/manage/add-change-subscription-administrator.md)。 

- 您的訂用帳戶必須向[Microsoft Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)註冊。 

- 您需要安裝 [Azure PowerShell](/powershell/azure) 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

- 您的 VM 資源必須位於[支援自訂計量的區域](metrics-custom-overview.md#supported-regions)中。

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>建立傳統虛擬機器和儲存體帳戶

1. 使用 Azure 入口網站建立傳統 VM。
   ![建立傳統 VM](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. 建立此 VM 時，請選擇建立新傳統儲存體帳戶的選項。 我們會在稍後步驟中使用此儲存體帳戶。

1. 在 Azure 入口網站中，移至 [儲存體帳戶]**** 資源刀鋒視窗。 選取 [金鑰]****，並記下儲存體帳戶名稱和儲存體帳戶金鑰。 稍後的步驟將會需要這項資訊。
   ![儲存體存取金鑰](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>建立服務主體

使用[建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)中的指示，在您的 Azure Active Directory 租用戶中建立服務主體。 進行此流程時，請注意下列事項： 
- 為此應用程式建立新用戶端密碼。
- 儲存金鑰與用戶端識別碼，以便在稍後的步驟中使用。

將您希望發出計量的資源權限授予此應用程式「監視計量發行者」。 您可以使用資源群組或整個訂用帳戶。  

> [!NOTE]
> 診斷擴充功能會使用服務主體，對 Azure 監視器進行驗證並對傳統 VM 發出計量。

## <a name="author-diagnostics-extension-configuration"></a>製作診斷擴充功能組態

1. 準備診斷擴充功能組態檔。 此檔案指定診斷擴充功能應為您傳統 VM 收集的記錄與效能計數器。 以下是範例：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. 在診斷檔案的 “SinksConfig” 區段中，定義新的 Azure 監視器接收，如下所示：

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. 在組態檔列出所要收集效能計數器的區段中，將效能計數器路由傳送到 Azure 監視器接收 "AzMonSink"。

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. 在私人組態中，定義 Azure 監視器帳戶。 然後新增服務主體資訊，用於發出計量。

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. 將此檔案儲存在本機。

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>將診斷擴充功能部署到您的雲端服務

1. 啟動 PowerShell 並登入。

    ```powershell
    Login-AzAccount
    ```

1. 首先針對傳統 VM 設定內容。

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. 設定以 VM 建立的傳統儲存體帳戶的內容。

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  使用下列命令，將診斷檔案路徑設定為變數：

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  使用已設定 Azure 監視器接收的診斷檔案，為傳統 VM 準備更新。

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  執行下列命令，將更新部署至您的 VM：

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> 在安裝診斷擴充功能期間，仍會強制您提供儲存體帳戶。 診斷組態檔中指定的任何記錄或效能計數器，都會寫入指定的儲存體帳戶。

## <a name="plot-the-metrics-in-the-azure-portal"></a>在 Azure 入口網站中繪製計量

1.  移至 Azure 入口網站。 

1.  在左側功能表上，選取 [**監視]。**

1.  在 [監視]**** 刀鋒視窗上，選取 [計量]****。

    ![瀏覽計量](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. 在 [資源] 下拉式功能表中，選取您的傳統 VM。

1. 在 [命名空間] 下拉式功能表中，選取 [ **azure**]。

1. 在 [計量] 下拉式功能表中，選取 [ **Memory\committed bytes 使用中的位元組**]。
   ![計量繪圖](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>後續步驟
- 深入了解[自訂計量](metrics-custom-overview.md)。

