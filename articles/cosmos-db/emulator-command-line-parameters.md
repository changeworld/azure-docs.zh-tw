---
title: Azure Cosmos DB 模擬器的命令列和 PowerShell 參考
description: 瞭解 Azure Cosmos DB 模擬器的命令列參數、如何使用 PowerShell 控制模擬器，以及如何變更您可以在模擬器內建立的容器數目。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: f8bcadf25ac8e001657f2be012f99ddb507e672d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445175"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Azure Cosmos DB 模擬器的命令列和 PowerShell 參考

Azure Cosmos 模擬器提供一個模擬 Azure Cosmos DB 服務的本機環境，以供本機開發之用。 [安裝模擬器](local-emulator.md)之後，您可以使用命令列和 PowerShell 命令來控制模擬器。 本文說明如何使用命令列和 PowerShell 命令來啟動和停止模擬器、設定選項，以及執行其他作業。 您必須從安裝位置執行命令。

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>使用命令列語法管理模擬器

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

若要檢視選項清單，請在命令提示字元輸入 `Microsoft.Azure.Cosmos.Emulator.exe /?` 。

|**選項** | **說明** | **命令**| **引數**|
|---|---|---|---|
|[無引數] | 使用預設設定啟動 Azure Cosmos 模擬器。 |Microsoft.Azure.Cosmos.Emulator.exe| |
|[說明] |顯示支援的命令列引數清單。|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |取得 Azure Cosmos 模擬器的狀態。 狀態會以結束代碼表示：1 = 啟動，2 = 執行中，3 = 已停止。 負數的結束代碼表示發生錯誤。 不會產生其他輸出。 | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| 關閉 Azure Cosmos 模擬器。| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|資料路徑 | 指定用來儲存資料檔案的路徑。 預設值為 %LocalAppdata%\CosmosDBEmulator。 | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>:可存取的路徑 |
|連接埠 | 指定用於模擬器的連接埠號碼。 預設值為 8081。 |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>:單一連接埠號碼 |
| ComputePort | 指定用於計算 Interop 閘道服務的連接埠號碼。 閘道 HTTP 端點探查連接埠的計算方式為 ComputePort + 79。 因此，ComputePort 和 ComputePort + 79 必須開啟且可供使用。 預設值為 8900。 | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>:單一連接埠號碼 |
| EnableMongoDbEndpoint=3.2 | 啟用 MongoDB API 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | 啟用 MongoDB API 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | 指定要用於 MongoDB 相容性 API 的連接埠號碼。 預設值為 10255。 |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>:單一連接埠號碼|
| EnableCassandraEndpoint | 啟用 Cassandra API | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | 指定用於 Cassandra 端點的連接埠號碼。 預設值為 10350。 | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>:單一連接埠號碼 |
| EnableGremlinEndpoint | 啟用 Gremlin API | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | 用於 Gremlin 端點的連接埠號碼。 預設值為 8901。 | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>:單一連接埠號碼 |
|EnableTableEndpoint | 啟用 Azure 資料表 API | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | 用於 Azure 資料表端點的連接埠號碼。 預設值為 8902。 | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>:單一連接埠號碼|
| KeyFile | 從指定的檔案中讀取授權金鑰。 使用 /GenKeyFile 選項來產生金鑰檔 | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>:檔案的路徑 |
| ResetDataPath | 以遞迴方式移除指定路徑中的所有檔案。 如果您未指定路徑，則會預設為 %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>:檔案路徑  |
| StartTraces  |  使用 LOGMAN 開始收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | 使用 LOGMAN 停止收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  使用 Windows 效能記錄工具開始收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | 使用 Windows 效能記錄工具停止收集偵錯追蹤記錄。 | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | 根據預設，模擬器會重新產生自我簽署的 TLS/SSL 憑證（如果憑證的 SAN 不包含模擬器主機的功能變數名稱、本機 IPv4 位址、' localhost ' 和 ' 127.0.0.1 '）。 使用此選項，模擬器反而將在啟動時失敗。 您接著應該使用 /GenCert 選項，來建立並安裝新的自我簽署 TLS/SSL 憑證。 | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | 產生並安裝新的自我簽署 TLS/SSL 憑證。 選擇性地包含以逗號分隔的其他 DNS 名稱清單，以透過網路存取模擬器。 | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>:其他 dns 名稱的選擇性逗號分隔清單  |
| DirectPorts |指定要用於直接連線的連接埠。 預設值為 10251、10252、10253、10254。 | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>:以逗號分隔的 4 個連接埠清單 |
| Key |模擬器的授權金鑰。 金鑰必須是 64 位元組向量的 base-64 編碼方式。 | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>:金鑰必須是 64 位元組向量的 base-64 編碼方式|
| EnableRateLimiting | 指定要啟用要求率限制行為。 |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |指定要停用要求率限制行為。 |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | 不要顯示模擬器使用者介面。 | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | 啟動時不要顯示資料總管。 |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | 指定已分割容器的數目上限。 如需詳細資訊，請參閱[變更容器的數目](#set-partitioncount)。 | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>:允許的單一分割區容器數目上限。 預設值為 25。 允許的上限為 250。|
| DefaultPartitionCount| 指定已分割容器之分割區數目的預設值。 | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> 預設值為 25。|
| AllowNetworkAccess | 讓模擬器可以透過網路存取。 您也必須傳遞 /Key=\<key_string\> 或 /KeyFile=\<file_name\> 才能啟用網路存取。 | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> 或 Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | 不要在使用 /AllowNetworkAccess 選項時調整防火牆規則。 |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | 產生新的授權金鑰並儲存到指定的檔案。 產生的金鑰可以搭配 /Key 或 /KeyFile 選項使用。 | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| 一致性 | 設定帳戶的預設一致性層級。 | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>:值必須屬於下列其中一個[一致性層級](consistency-levels.md)：Session、Strong、Eventual 或 BoundedStaleness。 預設值為 Session。 |
| ? | 顯示說明訊息。| | |

## <a name="manage-the-emulator-with-powershell"></a>使用 PowerShell 管理模擬器

模擬器隨附 PowerShell 模組，可用於啟動、停止、解除安裝及擷取服務的狀態。 執行下列 Cmdlet 以使用 PowerShell 模組：

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

或者，將 `PSModules` 目錄放置於您的 `PSModulesPath` 並匯入它，如下列命令所示：

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

以下是從 PowerShell 控制模擬器的命令摘要：

### `Get-CosmosDbEmulatorStatus`

**語法**

`Get-CosmosDbEmulatorStatus`

**備註**

傳回下列其中一個 ServiceControllerStatus 值：ServiceControllerStatus.StartPending、ServiceControllerStatus.Running 或 ServiceControllerStatus.Stopped。

### `Start-CosmosDbEmulator`

**語法**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**備註**

啟動模擬器。 根據預設，命令會等到模擬器準備好接受要求。 如果您希望啟動模擬器時立即傳回 Cmdlet，請使用 -NoWait 選項。

### `Stop-CosmosDbEmulator`

**語法**

 `Stop-CosmosDbEmulator [-NoWait]`

**備註**

停止模擬器。 根據預設，此命令會等到模擬器完全關機。 如果您希望模擬器開始關閉時立即傳回 Cmdlet，請使用 -NoWait 選項。

### `Uninstall-CosmosDbEmulator`

**語法**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**備註**

解除安裝模擬器，並選擇性地移除 $env 的完整內容：LOCALAPPDATA\CosmosDbEmulator。
Cmdlet 可確保在解除安裝之前停止模擬器。

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>變更預設容器的數目

根據預設，您最多可以建立 25 個固定大小的容器 (僅支援使用 Azure Cosmos DB SDK)，或者，使用 Azure Cosmos 模擬器來建立 5 個無限制的容器。 藉由修改 **PartitionCount** 值，您最多可以建立 250 個固定大小的容器、50 個無限制的容器，或兩者不超過 250 個固定大小容器的任意組合 (其中一個無限制的容器 = 5 個固定大小的容器)。 但是，不建議將模擬器設定為搭配超過 200 個固定大小的容器來執行。 由於它對磁碟 IO 作業新增了額外負荷，因此，會在使用端點 API 時導致無法預期的逾時。

如果您在超過目前分割區計數之後嘗試建立容器，模擬器就會擲回 ServiceUnavailable 例外狀況，並隨附下列訊息。

> 抱歉，我們目前在此區域中遇到高需求，且目前無法完成您的要求。 我們正持續努力讓越來越多容量上線，並建議您再試一次。
> ActivityId：12345678-1234-1234-1234-123456789abc

若要變更 Azure Cosmos 模擬器中可用的容器數目，請執行下列步驟：

1. 以滑鼠右鍵按一下系統匣上的 **Azure Cosmos DB 模擬器** ] 圖示，然後按一下 [ **重設資料**]，以刪除所有本機 Azure Cosmos 模擬器資料 .。。

1. 刪除此資料夾 `%LOCALAPPDATA%\CosmosDBEmulator` 中的所有模擬器資料。

1. 結束所有開啟的執行個體，方法是以滑鼠右鍵按一下系統匣上的 [Azure Cosmos DB 模擬器] 圖示，然後按一下 [結束]。 結束所有執行個體可能需要數分鐘的時間。

1. 安裝最新版的 [Azure Cosmos 模擬器](https://aka.ms/cosmosdb-emulator)。

1. 啟動具有 PartitionCount 旗標的模擬器，方法是設定值 <= 250。 例如： `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100` 。
 
## <a name="next-steps"></a>後續步驟

* [匯出 Azure Cosmos 模擬器憑證以與 JAVA、Python 和 Node.js apps 搭配使用](local-emulator-export-ssl-certificates.md)
* [模擬器的偵錯工具問題](troubleshoot-local-emulator.md)
