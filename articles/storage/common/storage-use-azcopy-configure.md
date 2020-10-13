---
title: 使用 Azure 儲存體設定、優化和疑難排解 AzCopy |Microsoft Docs
description: 使用 Azure 儲存體設定、優化和疑難排解 AzCopy。 變更或移除方案和記錄檔的位置。 變更預設記錄層級。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 9742f97832c1fc931a1679132e262f92c9f11225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037179"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>對 AzCopy 進行設定、最佳化及疑難排解

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 Blob 或檔案。 本文可協助您執行進階的組態工作，並協助您針對使用 AzCopy 時可能發生的問題進行疑難排解。

> [!NOTE]
> 如果您要尋找可協助您開始使用 AzCopy 的內容，請參閱下列文章：
> - [開始使用 AzCopy](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
> - [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要設定 AzCopy 的 proxy 設定，請設定 `https_proxy` 環境變數。 如果您在 Windows 上執行 AzCopy，AzCopy 會自動偵測 Proxy 設定，因此您不需要在 Windows 中使用此設定。 如果您選擇在 Windows 中使用此設定，其將會覆寫自動偵測。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | 在命令提示字元中，請使用：`set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中，請使用：`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **macOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前，AzCopy 不支援需要使用 NTLM 或 Kerberos 進行驗證的 proxy。

### <a name="bypassing-a-proxy"></a>略過 proxy ###

如果您是在 Windows 上執行 AzCopy，而您想要告訴它在所有 (使用 proxy， _而不是_ 自動偵測設定) 使用這些命令。 使用這些設定時，AzCopy 將不會查閱或嘗試使用任何 proxy。

| 作業系統 | 環境 | 命令  |
|--------|-----------|----------|
| **Windows** | 命令提示字元 (CMD)  | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

如果您不想要使用 PROXY，請在其他作業系統上，將 HTTPS_PROXY 變數保持未設定。

## <a name="optimize-performance"></a>效能最佳化

您可以效能評定效能，然後使用命令和環境變數來尋找效能與資源耗用量之間的最佳折衷。

本節可協助您執行這些優化工作：

> [!div class="checklist"]
> * 執行基準測試
> * 輸送量最佳化
> * 優化記憶體使用 
> * 優化檔案同步處理

### <a name="run-benchmark-tests"></a>執行基準測試

您可以在特定的 blob 容器或檔案共用上執行效能基準測試，以查看一般效能統計資料和識別效能瓶頸。 您可以上傳或下載產生的測試資料來執行測試。 

使用下列命令來執行效能基準測試。

|    |     |
|--------|-----------|
| **語法** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **範例** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

此命令會將測試資料上傳至指定的目的地，以執行效能基準測試。 測試資料會在記憶體中產生、上傳至目的地，然後在測試完成後從目的地刪除。 您可以使用選擇性的命令參數，指定要產生的檔案數目，以及您想要的大小。

如果您想要藉由下載資料來執行這項測試，請將 `mode` 參數設定為 `download` 。 如需詳細的參考檔，請參閱 [azcopy 基準測試](storage-ref-azcopy-bench.md)。 

### <a name="optimize-throughput"></a>輸送量最佳化

您可以 `cap-mbps` 在命令中使用旗標，以提高輸送量資料速率的上限。 例如，下列命令會繼續執行作業，並將 `10` 每秒 mb 的輸送量 (Mb) 。 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

傳輸小型檔案時，輸送量可能會降低。 您可以藉由設定環境變數來增加輸送量 `AZCOPY_CONCURRENCY_VALUE` 。 此變數會指定可發生的並行要求數。  

如果您的電腦有少於5個 Cpu，此變數的值會設為 `32` 。 否則，預設值會等於 16 乘以 CPU 數目。 這個變數的最大預設值為 `3000` ，但您可以手動將這個值設定為較高或較低。 

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用 `azcopy env` 檢查此變數目前的值。 如果值是空白的，您可以查看任何 AzCopy 記錄檔的開頭，以讀取正在使用的值。 系統會報告選取的值，以及其選取的原因。

在設定此變數之前，建議您先執行基準測試。 基準測試流程將會報告建議的並行值。 或者，如果您的網路狀況和承載不同，請將此變數設定為單字， `AUTO` 而不是特定的數位。 這會導致 AzCopy 一律執行其在基準測試測試中所使用的相同自動調整進程。

### <a name="optimize-memory-use"></a>優化記憶體使用

設定 `AZCOPY_BUFFER_GB` 環境變數，以指定您想要 AzCopy 在下載和上傳檔案時使用的系統記憶體數量上限。
以 gb (GB) 表示此值。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>優化檔案同步處理

[Sync](storage-ref-azcopy-sync.md)命令會識別目的地上的所有檔案，然後在開始同步處理作業之前，比較檔案名和上次修改的時間戳記。 如果您有大量的檔案，則可以藉由消除此預先處理來改善效能。 

若要完成此動作，請改用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，並將 `--overwrite` 旗標設為 `ifSourceNewer` 。 AzCopy 會比較檔案，因為它們會複製，而不會執行任何前置掃描和比較。 如果有大量檔案要比較，這會提供效能邊緣。

[Azcopy 複製](storage-ref-azcopy-copy.md)命令不會刪除目的地中的檔案，因此，如果您想要在目的地不再存在於來源的檔案時將其刪除，請使用[azcopy 同步](storage-ref-azcopy-sync.md)命令，並將 `--delete-destination` 旗標設為或的 `true` 值 `prompt` 。 

## <a name="troubleshoot-issues"></a>針對問題進行疑難排解

AzCopy 會建立為每個作業記錄和方案檔案。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 

記錄檔將包含失敗的狀態 (`UPLOADFAILED` 、 `COPYFAILED` 和 `DOWNLOADFAILED`) 、完整路徑，以及失敗的原因。

根據預設，記錄檔和計畫檔案位於 `%USERPROFILE%\.azcopy` Windows 上的目錄或 `$HOME$\.azcopy` Mac 和 Linux 上的目錄，但您可以視需要變更該位置。

相關的錯誤不一定是出現在檔案中的第一個錯誤。 針對網路錯誤、超時和伺服器忙碌錯誤等錯誤，AzCopy 會重試最多20次，而且通常重試程式會成功。  您看到的第一個錯誤可能是已成功重試的一些無害。  因此，請尋找接近、或的錯誤，而不是查看檔案中的第一個 `UPLOADFAILED` 錯誤 `COPYFAILED` `DOWNLOADFAILED` 。 

> [!IMPORTANT]
> 提交要求給 Microsoft 支援服務 (或針對涉及任何協力廠商) 的問題進行疑難排解時，請共用您要執行之命令的修訂版本。 這可確保 SAS 不會不慎與任何人共用。 您可以在記錄檔開頭找到編校的版本。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會從記錄檔中取得所有狀態的錯誤 `UPLOADFAILED` `04dc9ca9-158f-7945-5933-564021086c79` ：

**Windows (PowerShell) **

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 使用下列命令來查看作業的歷程記錄：

```
azcopy jobs list
```

若要檢視作業統計資料，請使用下列命令：

```
azcopy jobs show <job-id>
```

若要依狀態篩選傳輸，請使用下列命令：

```
azcopy jobs show <job-id> --with-status=Failed
```

使用下列命令來繼續失敗/已取消的工作。 此命令會使用其識別碼以及 SAS 權杖，因為它不會因為安全考慮而持續存在：

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> 以單引號括住路徑引數，例如 ( ' ' ) 的 SAS 權杖。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

當您繼續工作時，AzCopy 會查看作業計畫檔。 方案檔會列出第一次建立作業時所識別要處理的所有檔案。 當您繼續工作時，AzCopy 會嘗試傳送尚未傳送的方案檔案中列出的所有檔案。

## <a name="change-the-location-of-the-plan-and-log-files"></a>變更方案和記錄檔的位置

根據預設，方案和記錄檔位於 `%USERPROFILE%\.azcopy` Windows 上的目錄，或 `$HOME$\.azcopy` Mac 和 Linux 上的目錄中。 您可以變更此位置。

### <a name="change-the-location-of-plan-files"></a>變更方案檔的位置

使用這些命令的任何一個。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> 在命令提示字元中，使用：： `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

使用 `azcopy env` 檢查此變數目前的值。 如果值是空白的，則會將計畫檔案寫入至預設位置。

### <a name="change-the-location-of-log-files"></a>變更記錄檔的位置

使用這些命令的任何一個。

| 作業系統 | Command  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_LOG_LOCATION="<value>"` <br> 在命令提示字元中，使用：： `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用 `azcopy env` 檢查此變數目前的值。 如果值是空白的，則會將記錄寫入至預設位置。

## <a name="change-the-default-log-level"></a>變更預設記錄層級

依預設，AzCopy 記錄層級會設定為 `INFO` 。 如果您想要減少記錄檔詳細資訊以節省磁碟空間，請使用選項覆寫此設定 ``--log-level`` 。 

可用的記錄層級為： `NONE` 、 `DEBUG` 、、、、 `INFO` `WARNING` `ERROR` `PANIC` 和 `FATAL` 。

## <a name="remove-plan-and-log-files"></a>移除方案和記錄檔

如果您想要從本機電腦移除所有方案和記錄檔，以節省磁碟空間，請使用 `azcopy jobs clean` 命令。

若只要移除與單一作業相關聯的計畫和記錄檔，請使用 `azcopy jobs rm <job-id>` 。 將 `<job-id>` 此範例中的預留位置取代為作業的工作識別碼。


