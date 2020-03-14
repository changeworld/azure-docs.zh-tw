---
title: Windows 診斷延伸模組架構
description: Azure 監視器中 Windows 診斷擴充功能（WAD）的設定架構參考。
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 4c711e1b0a63fbcf978c0e4467eadaed8d91f3de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274706"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows 診斷延伸模組架構
Azure 診斷延伸模組是 Azure 監視器中的代理程式，可從客體作業系統和 Azure 計算資源的工作負載收集監視資料。 本文詳細說明在 Windows 虛擬機器和其他計算資源上設定診斷擴充功能時所使用的架構。

> [!NOTE]
> 本文中的架構適用于1.3 和更新版本（Azure SDK 2.4 和更新版本）。 我們會在較新的組態區段中加入標記，表示已將它們新增至哪一個版本中。 架構的版本1.0 和1.2 已封存，已無法再使用。 

## <a name="public-configuration-file-schema"></a>公用設定檔架構

執行下列 PowerShell 命令，以下載公用組態檔結構描述定義：  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>一般屬性類型  
 **scheduledTransferPeriod** 屬性會出現在數個元素中。 其為排程傳輸至儲存體之間的間隔，無條件進位到最接近的分鐘數。 值是 [XML「持續時間資料類型」(英文)](https://www.w3schools.com/xml/schema_dtypes_date.asp)。


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration

已在 1.3 版中新增。  

診斷組態檔的最上層元素。  

**屬性** xmlns - 適用於診斷組態檔的 XML 命名空間如下：  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|子元素|描述|  
|--------------------|-----------------|  
|**PublicConfig**|必要。 請參閱本頁面上其他部分的說明。|  
|**PrivateConfig**|選擇性。 請參閱本頁面上其他部分的說明。|  
|**IsEnabled**|布林值。 請參閱本頁面上其他部分的說明。|  

## <a name="publicconfig-element"></a>PublicConfig 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig

 說明公用診斷組態。  

|子元素|描述|  
|--------------------|-----------------|  
|**WadCfg**|必要。 請參閱本頁面上其他部分的說明。|  
|**StorageAccount**|要儲存資料的 Azure 儲存體帳戶名稱。 可能也會在執行 Set-AzureServiceDiagnosticsExtension Cmdlet 時指定為參數。|  
|**StorageType**|可以是 Table、Blob 或 TableAndBlob。 預設值是 Table。 若選擇 TableAndBlob，系統會將診斷資料寫入兩次 -- 每種類型寫入一次。|  
|**LocalResourceDirectory**|虛擬機器上監視代理程式儲存事件資料的目錄。 如果沒有，請設定，否則會使用預設的目錄：<br /><br /> 針對背景工作/web 角色：`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> 針對虛擬機器：`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 必要屬性包括：<br /><br /> - **path** - 系統上 Azure 診斷所使用的目錄。<br /><br /> - **expandEnvironment** - 控制是否要展開路徑名稱中的環境變數。|  

## <a name="wadcfg-element"></a>WadCFG 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG

 識別並設定要收集的遙測資料。  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 元素
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration

 必要

|屬性|描述|  
|----------------|-----------------|  
| **overallQuotaInMB** | 可供 Azure 診斷所收集的各種類型診斷資料取用的本機磁碟空間量上限。 預設設定為 4096 MB。<br />
|**useProxyServer** | 設定 Azure 診斷來使用 Proxy 伺服器設定，如 IE 設定中所設定。|
|**sinks** | 在 1.5 中新增。 選擇性。 針對支援接收的所有子元素，同時要傳送診斷資料的接收位置指標。 接收範例為 Application Insights 或事件中樞。|  


<br /> <br />

|子元素|描述|  
|--------------------|-----------------|  
|**CrashDumps**|請參閱本頁面上其他部分的說明。|  
|**DiagnosticInfrastructureLogs**|啟用收集 Azure 診斷所產生的記錄。 診斷基礎結構記錄適用於疑難排解診斷系統本身。 選用屬性包括：<br /><br /> - **scheduledTransferLogLevelFilter** - 設定所收集之記錄的最低嚴重性層級。<br /><br /> - **scheduledTransferPeriod** - 排程傳輸至儲存體之間的間隔，無條件進位到最接近的分鐘數。 值是 [XML「持續時間資料類型」(英文)](https://www.w3schools.com/xml/schema_dtypes_date.asp)。 |  
|**Directories**|請參閱本頁面上其他部分的說明。|  
|**EtwProviders**|請參閱本頁面上其他部分的說明。|  
|**計量**|請參閱本頁面上其他部分的說明。|  
|**PerformanceCounters**|請參閱本頁面上其他部分的說明。|  
|**WindowsEventLog**|請參閱本頁面上其他部分的說明。|
|**DockerSources**|請參閱本頁面上其他部分的說明。 |



## <a name="crashdumps-element"></a>CrashDumps 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps

 啟用收集損毀傾印。  

|屬性|描述|  
|----------------|-----------------|  
|**containerName**|選擇性。 在您的 Azure 儲存體帳戶中用來儲存損毀傾印的 Blob 容器名稱。|  
|**crashDumpType**|選擇性。  設定 Azure 診斷來收集迷你或完整的損毀傾印。|  
|**directoryQuotaPercentage**|選擇性。  設定要在 VM 上保留以供損毀傾印使用的 **overallQuotaInMB** 百分比。|  

|子元素|描述|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|必要。 定義每個處理序的組態值。<br /><br /> 以下也是必要屬性：<br /><br /> **processName** - 您希望 Azure 診斷收集損毀傾印的處理序名稱。|  

## <a name="directories-element"></a>Directories 元素
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories

 啟用收集目錄、IIS 失敗的存取要求記錄和/或 IIS 記錄的內容。  

 選用的 **scheduledTransferPeriod** 屬性。 請參閱稍早的說明。  

|子元素|描述|  
|--------------------|-----------------|  
|**IISLogs**|在組態中包含此元素，就能收集 IIS 記錄：<br /><br /> **containerName** - 在您的 Azure 儲存體帳戶中用來儲存 IIS 記錄的 Blob 容器名稱。|   
|**FailedRequestLogs**|在組態中包含此元素，就能夠收集對於 IIS 站台或應用程式之失敗要求的相關記錄。 您也必須在 **Web.config** 的 **system.WebServer** 下啟用追蹤選項。|  
|**DataSources**|要監視的目錄清單。|




## <a name="datasources-element"></a>DataSources 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources

 要監視的目錄清單。  

|子元素|描述|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|必要。 必要屬性：<br /><br /> **containerName** - 在您的 Azure 儲存體帳戶中用來儲存記錄檔的 Blob 容器名稱。|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration

 可能包括 **Absolute** 或 **LocalResource** 元素，但非兩者。  

|子元素|描述|  
|--------------------|-----------------|  
|**Absolute**|要監視之目錄的絕對路徑。 以下為必要屬性：<br /><br /> - **Path** - 要監視之目錄的絕對路徑。<br /><br /> - **expandEnvironment** - 設定是否要展開 Path 中的環境變數。|  
|**LocalResource**|相對於要監視之本機資源的路徑。 必要屬性包括：<br /><br /> - **Name** - 包含要監視之目錄的本機資源<br /><br /> - **relativePath** - 包含要監視目錄之 Name 的相對路徑|  



## <a name="etwproviders-element"></a>EtwProviders 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders

 設定要收集來自 EventSource 和/或以 ETW 資訊清單為基礎之提供者的 ETW 事件。  

|子元素|描述|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|設定要收集從 [EventSource 類別](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)產生的事件。 必要屬性：<br /><br /> **provider** - EventSource 事件的類別名稱。<br /><br /> 選用屬性包括：<br /><br /> - **scheduledTransferLogLevelFilter** - 要傳輸至儲存體帳戶的最低嚴重性層級。<br /><br /> - **scheduledTransferPeriod** - 排程傳輸至儲存體之間的間隔，無條件進位到最接近的分鐘數。 值是 [XML「持續時間資料類型」(英文)](https://www.w3schools.com/xml/schema_dtypes_date.asp)。 |  
|**EtwManifestProviderConfiguration**|必要屬性：<br /><br /> **provider** - 事件提供者的 GUID<br /><br /> 選用屬性包括：<br /><br /> - **scheduledTransferLogLevelFilter** - 要傳輸至儲存體帳戶的最低嚴重性層級。<br /><br /> - **scheduledTransferPeriod** - 排程傳輸至儲存體之間的間隔，無條件進位到最接近的分鐘數。 值是 [XML「持續時間資料類型」(英文)](https://www.w3schools.com/xml/schema_dtypes_date.asp)。 |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration

 設定要收集從 [EventSource 類別](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)產生的事件。  

|子元素|描述|  
|--------------------|-----------------|  
|**DefaultEvents**|選用屬性：<br/><br/> **eventDestination** - 要儲存事件的資料表名稱|  
|**事件**|必要屬性：<br /><br /> **id** - 事件的識別碼。<br /><br /> 選用屬性：<br /><br /> **eventDestination** - 要儲存事件的資料表名稱|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration

|子元素|描述|  
|--------------------|-----------------|  
|**DefaultEvents**|選用屬性：<br /><br /> **eventDestination** - 要儲存事件的資料表名稱|  
|**事件**|必要屬性：<br /><br /> **id** - 事件的識別碼。<br /><br /> 選用屬性：<br /><br /> **eventDestination** - 要儲存事件的資料表名稱|  



## <a name="metrics-element"></a>Metrics 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics

 讓您能夠產生已最佳化的效能計數器資料表來進行快速查詢。 除了效能計數器資料表之外，**PerformanceCounters** 元素中所定義的每個效能計數器都會儲存於 Metrics 資料表中。  

 **resourceId** 是必要屬性。  您要部署 Azure 診斷的虛擬機器或虛擬機器擴展集資源識別碼。 從 **Azure 入口網站**取得 [resourceID](https://portal.azure.com)。 選取 [瀏覽][資源群組] ->  ->  **<Name\>** 。 按一下 [屬性] 圖格，並複製 [識別碼] 欄位的值。  

|子元素|描述|  
|--------------------|-----------------|  
|**MetricAggregation**|必要屬性：<br /><br /> **scheduledTransferPeriod** - 排程傳輸至儲存體之間的間隔，無條件進位到最接近的分鐘數。 值是 [XML「持續時間資料類型」(英文)](https://www.w3schools.com/xml/schema_dtypes_date.asp)。 |  



## <a name="performancecounters-element"></a>PerformanceCounters 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters

 啟用收集效能計數器。  

 選用屬性：  

 選用的 **scheduledTransferPeriod** 屬性。 請參閱稍早的說明。

|子元素|描述|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|以下為必要屬性：<br /><br /> - **counterSpecifier** - 效能計數器的名稱。 例如： `\Processor(_Total)\% Processor Time` 。 若要在主機上取得效能計數器清單，請執行 `typeperf` 命令。<br /><br /> - **sampleRate** - 應針對計數器進行取樣的頻率。<br /><br /> 選用屬性：<br /><br /> **unit** - 計數器的測量單位。 值可在[Unittype.pixel 表示類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet)取得 |
|**sinks** | 在 1.5 中新增。 選擇性。 同時要傳送診斷資料的接收位置指標。 例如，Azure 監視器或事件中樞。|    




## <a name="windowseventlog-element"></a>WindowsEventLog 元素
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog

 啟用收集 Windows 事件記錄。  

 選用的 **scheduledTransferPeriod** 屬性。 請參閱稍早的說明。  

|子元素|描述|  
|-------------------|-----------------|  
|**DataSource**|要收集的 Windows 事件記錄。 必要屬性：<br /><br /> **name** - 說明要收集之 Windows 事件的 XPath 查詢。 例如：<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> 若要收集所有事件，請指定 "*"|  




## <a name="logs-element"></a>Logs 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs

 出現在 1.0 和 1.1 版中。 在 1.2 中消失。 再度新增於 1.3 中。  

 定義基本 Azure 記錄的緩衝區組態。  

|屬性|類型|描述|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|選擇性。 指定適用於所指定資料的檔案系統儲存體數量上限。<br /><br /> 預設值是 0。|  
|**scheduledTransferLogLevelFilter**|**string**|選擇性。 指定所傳輸記錄項目的最低嚴重性層級。 預設值為 **Undefined**，會傳輸所有記錄。 其他可能的值 (按照從大到小的順序排列) 為 **Verbose**、**Information**、**Warning**、**Error** 及 **Critical**。|  
|**scheduledTransferPeriod**|**duration**|選擇性。 指定排程傳輸資料之間的間隔，無條件進位到最接近的分鐘數。<br /><br /> 預設值為 PT0S。|  
|**sinks** |**string**| 在 1.5 中新增。 選擇性。 同時要傳送診斷資料的接收位置指標。 例如，Application Insights 或事件中樞。|  

## <a name="dockersources"></a>DockerSources
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources

 已在 1.9 版中新增。

|元素名稱|描述|  
|------------------|-----------------|  
|**Stats**|會請系統收集 Docker 容器的統計資料|  

## <a name="sinksconfig-element"></a>SinksConfig 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig

 傳送診斷資料的位置清單，以及與這些位置相關聯的組態。  

|元素名稱|描述|  
|------------------|-----------------|  
|**接收**|請參閱本頁面上其他部分的說明。|  

## <a name="sink-element"></a>Sink 元素
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink

 已在 1.5 版中新增。  

 定義要傳送診斷資料的目標位置。 例如 Application Insights 服務。  

|屬性|類型|描述|  
|---------------|----------|-----------------|  
|**name**|字串|識別 sinkname 的字串。|  

|元素|類型|描述|  
|-------------|----------|-----------------|  
|**Application Insights**|字串|僅會在將資料傳送至 Application Insights 時使用。 包含您有權存取之使用中 Application Insights 帳戶的檢測金鑰。|  
|**Channels**|字串|每個可額外篩選該資料流的其中一個|  

## <a name="channels-element"></a>Channels 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels

 已在 1.5 版中新增。  

 針對通過接收之記錄資料的資料流定義篩選器。  

|元素|類型|描述|  
|-------------|----------|-----------------|  
|**通路**|字串|請參閱本頁面上其他部分的說明。|  

## <a name="channel-element"></a>Channel 元素
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel

 已在 1.5 版中新增。  

 定義要傳送診斷資料的目標位置。 例如 Application Insights 服務。  

|屬性|類型|描述|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|指定所傳輸記錄項目的最低嚴重性層級。 預設值為 **Undefined**，會傳輸所有記錄。 其他可能的值 (按照從大到小的順序排列) 為 **Verbose**、**Information**、**Warning**、**Error** 及 **Critical**。|  
|**name**|**string**|要參考之通道的唯一名稱|  


## <a name="privateconfig-element"></a>PrivateConfig 元素
 樹狀結構︰根目錄 - DiagnosticsConfiguration - PrivateConfig

 已在 1.3 版中新增。  

 選用  

 存放儲存體帳戶的私用詳細資料 (名稱、金鑰和端點)。 此資訊會傳送至虛擬機器，但無法從中擷取。  

|子元素|描述|  
|--------------------|-----------------|  
|**StorageAccount**|要使用的儲存體帳戶。 以下為必要屬性<br /><br /> - **name** - 儲存體帳戶的名稱。<br /><br /> - **key** - 儲存體帳戶的金鑰。<br /><br /> - **endpoint** - 要存取儲存體帳戶的端點。 <br /><br /> -**sasToken** （新增1.8.1）-您可以在私人設定中指定 SAS 權杖，而不是儲存體帳戶金鑰。如果有提供，則會忽略儲存體帳戶金鑰。 <br />SAS 權杖的需求︰ <br />- 僅支援帳戶 SAS 權杖 <br />- 需要 b、t 服務類型。 <br /> - 需要 a、c、u、w 權限。 <br /> - 需要 c、o 資源類型。 <br /> - 僅支援 HTTPS 通訊協定 <br /> - 開始和到期時間必須是有效的。|  


## <a name="isenabled-element"></a>IsEnabled 元素  
 樹狀結構︰根目錄 - DiagnosticsConfiguration - IsEnabled

 布林值。 使用 `true` 來啟用診斷或 `false` 來停用診斷。

## <a name="example-configuration"></a>範例設定
 以下是在 JSON 和 XML 中顯示之 Windows 診斷擴充功能的完整範例設定。

 
### <a name="json"></a>JSON

*PublicConfig*和*PrivateConfig*是分開的，因為在大部分的 JSON 使用案例中，它們會當做不同的變數來傳遞。 這些案例包括 Resource Manager 範本、PowerShell 和 Visual Studio。

> [!NOTE]
> 公用設定 Azure 監視器接收定義具有兩個屬性： *resourceId*和*region*。 這些只有傳統 VM 和傳統雲端服務才需要使用。 這些屬性不應該用於其他資源。

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> 私用設定 Azure 監視器接收定義具有兩個屬性： *PrincipalId*和*Secret*。 這些只有傳統 VM 和傳統雲端服務才需要使用。 這些屬性不應該用於其他資源。


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> 公用設定 Azure 監視器接收定義具有兩個屬性：resourceId 與 region。 這些只有傳統 VM 和傳統雲端服務才需要使用。 這些屬性不應該用於 Resource Manager 虛擬機器或虛擬機器擴展集。
> Azure 監視器接收另外還有一個私用設定項目，可傳入主體識別碼和密碼。 這只有傳統 VM 和傳統雲端服務才需要使用。 對於 Resource Manager VM 與 VMSS，可排除私用設定項目中的 Azure 監視器定義。
>
