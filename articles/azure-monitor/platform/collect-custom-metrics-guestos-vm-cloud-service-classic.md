---
title: 將傳統雲端服務計量傳送至 Azure 監視器計量資料庫
description: 說明將 Azure 傳統雲端服務的來賓 OS 效能計量傳送至 Azure 監視器計量存放區的程式。
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655784"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>將客體作業系統計量傳送至 Azure 監視器計量存放區的傳統雲端服務 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您可以使用 Azure 監視器[診斷擴充功能](diagnostics-extension-overview.md)，從當作虛擬機器、雲端服務或 Service Fabric 叢集一部分執行的客體作業系統 (客體 OS) 收集計量與記錄。 擴充功能可以將遙測資料傳送到[許多不同位置](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)。

本文說明將 Azure 傳統雲端服務的客體 OS 效能計量傳送至 Azure 監視器計量存放區的程序。 從診斷 1.11 版開始，您可以直接將計量寫入到已收集標準平台計量的 Azure 監視器計量存放區。 

將計量儲存在此位置，可讓您存取與您可對平台計量執行的相同動作。 動作包括近乎即時的警示、圖表、路由、從 REST API 存取以及更多功能。  在過去，診斷擴充功能會寫入到 Azure 儲存體，而不是 Azure 監視器資料存放區。  

本文中所述的程序僅適用於 Azure 雲端服務中的效能計數器。 不適合用於其他自訂計量。 

## <a name="prerequisites"></a>先決條件

- 您必須是 Azure 訂用帳戶的[服務管理員或共同管理員](../../cost-management-billing/manage/add-change-subscription-administrator.md)。 

- 您的訂用帳戶必須向[Microsoft Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)註冊。 

- 您需要安裝 [Azure PowerShell](/powershell/azure) 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

- 您的雲端服務必須位於[支援自訂計量的區域](metrics-custom-overview.md#supported-regions)中。

## <a name="provision-a-cloud-service-and-storage-account"></a>佈建雲端服務與儲存體帳戶 

1. 建立及部署傳統雲端服務。 您可以在[開始使用 Azure 雲端服務和 ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md) 找到範例傳統雲端服務應用程式和部署。 

2. 您可以使用現有的儲存體帳戶或部署新的儲存體帳戶。 儲存體帳戶最好能與您建立的傳統雲端服務位於相同的區域中。 在 Azure 入口網站中，移至 [儲存體帳戶]**** 資源刀鋒視窗，然後選取 [金鑰]****。 記下儲存體帳戶名稱和儲存體帳戶金鑰。 稍後的步驟將會需要這項資訊。

   ![儲存體帳戶金鑰](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>建立服務主體 

使用[使用入口網站來建立可存取資源的 Azure Active Directory 應用程式和服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)中的指示，在 Azure Active Directory 租用戶中建立服務主體。 進行此流程時，請注意下列事項： 

- 您可以將任何 URL 填入為登入 URL。  
- 為此應用程式建立新用戶端密碼。  
- 儲存金鑰與用戶端識別碼，以便在稍後的步驟中使用。  

針對您希望發出計量的資源，將其「監視計量發行者」** 權限授與在上一個步驟中建立的應用程式。 如果您計劃使用該應用程式對許多資源發出自訂計量，您可以在資源群組或訂用帳戶層級上授與這些權限。  

> [!NOTE]
> 診斷擴充功能會使用服務主體，對 Azure 監視器驗證，並發出雲端服務的計量。

## <a name="author-diagnostics-extension-configuration"></a>製作診斷擴充功能組態 

準備診斷擴充功能組態檔。 此檔案指定診斷擴充功能應為雲端服務收集的記錄與效能計數器。 以下是範例診斷組態檔：  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

在診斷檔案的 "SinksConfig" 區段中，定義新的 Azure 監視器接收： 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

在組態檔列出所要收集之效能計數器的區段中，新增 Azure 監視器接收。 此項目可確保將您指定的所有效能計數器作為計量，路由傳送到 Azure 監視器。 您可以根據需求新增或移除效能計數器。 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

最後，在私人設定中，新增 [Azure 監視器帳戶]** 區段。 輸入先前建立的服務主體用戶端識別碼和密碼。 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

在本機儲存此診斷檔案。  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>將診斷擴充功能部署到您的雲端服務 

啟動 PowerShell 並登入 Azure。 

```powershell
Login-AzAccount 
```

使用下列命令來儲存您稍早建立的儲存體帳戶詳細資料。 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

同樣地，使用下列命令，將診斷檔案路徑設定為變數：

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

透過使用下列命令設定的 Azure 監視器接收，使用診斷檔案將診斷擴充功能部署到您的雲端服務：  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> 在安裝診斷擴充功能期間，仍會強制您提供儲存體帳戶。 診斷組態檔中指定的任何記錄或效能計數器，都會寫入指定的儲存體帳戶。  

## <a name="plot-metrics-in-the-azure-portal"></a>在 Azure 入口網站中繪製計量 

1. 移至 Azure 入口網站。 

   ![計量 Azure 入口網站](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. 在左側功能表上，選取 [**監視]。**

3. 在 [監視]**** 刀鋒視窗上，選取 [計量預覽]**** 索引標籤。

4. 在 [資源] 下拉式功能表中，選取您的傳統雲端服務。

5. 在 [命名空間] 下拉式功能表中，選取 [ **azure**]。 

6. 在 [計量] 下拉式功能表中，選取 [ **Memory\committed bytes 使用中的位元組**]。 

您可使用維度篩選與分割功能，檢視特定角色或角色執行個體所使用的記憶體總數。 

 ![計量 Azure 入口網站](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>後續步驟

- 深入了解[自訂計量](metrics-custom-overview.md)。

