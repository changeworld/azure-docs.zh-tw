---
title: 在 Azure 儲存體中儲存和檢視診斷資料
description: 了解如何收集 Azure 儲存體帳戶中的 Azure 診斷資料，以便使用數個可用工具的其中一個工具加以檢視。
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 9e16d77c36f382e72d5e9902b07afbbec50b6876
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723249"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>在 Azure 儲存體中儲存和檢視診斷資料
診斷資料不會永久儲存，除非您將它傳送至 Microsoft Azure 儲存體模擬器或 Azure 儲存體。 一旦位於儲存體，即可利用其中一個可用的工具進行檢視。

## <a name="specify-a-storage-account"></a>指定儲存體帳戶
您可指定您要在 ServiceConfiguration.cscfg 檔案中使用的儲存體帳戶。 帳戶資訊會定義為組態設定中的連接字串。 下列範例顯示在 Visual Studio 中為新的雲端服務專案建立的預設連接字串：

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

您可以變更此連接字串，以提供 Azure 儲存體帳戶的帳戶資訊。

視所收集的診斷資料類型而定，Azure 診斷會使用 Blob 服務或資料表服務。 下表顯示保存的資料來源以及其格式。

| 資料來源 | 儲存體格式 |
| --- | --- |
| Azure 記錄 |Table |
| IIS 7.0 記錄 |Blob |
| Azure 診斷基礎結構記錄 |Table |
| 失敗要求追蹤記錄 |Blob |
| Windows 事件記錄檔 |Table |
| 效能計數器 |Table |
| 損毀傾印 |Blob |
| 自訂錯誤記錄 |Blob |

## <a name="transfer-diagnostic-data"></a>傳輸診斷資料
若為 SDK 2.5 和更新版本，可以透過組態檔進行傳輸診斷資料的要求。 您可以依照組態中指定的排程間隔來傳輸診斷資料。

若為 SDK 2.4 和更舊版本，您可以透過組態檔以及程式設計方式要求傳輸診斷資料。 程式設計的方式也可讓您進行隨選傳輸。

> [!IMPORTANT]
> 當您將診斷資料傳輸至 Azure 儲存體帳戶時，您的診斷資料所用的儲存體資源就會發生成本。
> 
> 

## <a name="store-diagnostic-data"></a>儲存診斷資料
記錄檔資料會以下列名稱儲存在 Blob 或資料表儲存體中：

**資料表**

* **WadLogsTable** - 使用追蹤接聽程式在程式碼中寫入的記錄。
* **WADDiagnosticInfrastructureLogsTable** - 診斷監視器和組態變更。
* **WADDirectoriesTable** – 診斷監視器所監視的目錄。  這包括 IIS 記錄、IIS 失敗要求記錄和自訂目錄。  Blob 記錄檔的位置是在 [容器] 欄位中指定，而 Blob 的名稱則是在 RelativePath 欄位中指定。  AbsolutePath 欄位會指出檔案存在於 Azure 虛擬機器上的位置和名稱。
* **WADPerformanceCountersTable** - 效能計數器。
* **WADWindowsEventLogsTable** – Windows 事件記錄。

**Blob**

* **wad-control-container** – (僅適用於 SDK 2.4 和前一版) 包含可控制 Azure 診斷的 XML 組態檔。
* **wad-iis-failedreqlogfiles**：包含 IIS 失敗要求記錄檔中的資訊。
* **wad-iis-logfiles**：包含有關 IIS 記錄檔的資訊。
* **"custom"** – 自訂容器，該容器是以設定診斷監視器所監視的目錄為基礎。  將會在 WADDirectoriesTable 中指定此 Blob 容器的名稱。

## <a name="tools-to-view-diagnostic-data"></a>用來檢視診斷資料的工具
有數個工具可用來檢視傳輸至儲存體後的資料。 例如：

* Visual Studio 中的伺服器總管 - 如果您已安裝 Azure Tools for Microsoft Visual Studio，您可以在伺服器總管中使用 Azure 儲存體節點，從您的 Azure 儲存體帳戶檢視唯讀的 Blob 和資料表資料。 您可以從您的本機儲存體模擬器帳戶顯示資料，也可以從您為 Azure 建立的儲存體帳戶顯示資料。 如需詳細資訊，請參閱 [使用伺服器總管瀏覽和管理儲存體資源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 儲存體 Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個獨立應用程式，可讓您在 Windows、OSX 和 Linux 上輕鬆使用 Azure 儲存體資料。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包含 Azure 診斷管理員，可讓您檢視、下載及管理在 Azure 上執行的應用程式所收集的診斷資料。

## <a name="next-steps"></a>後續步驟
[使用 Azure 診斷追蹤雲端服務應用程式中的流程](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


