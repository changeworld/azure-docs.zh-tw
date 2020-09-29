---
title: 針對使用 Azure Cosmos 模擬器時的問題進行疑難排解
description: 瞭解如何在使用 Azure Cosmos 模擬器時，對服務無法使用、憑證、加密和版本控制問題進行疑難排解。
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: af9122aaa0233fe5248f31ffe805e01a98831eae
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447425"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>針對使用 Azure Cosmos 模擬器時的問題進行疑難排解

Azure Cosmos 模擬器提供一個模擬 Azure Cosmos DB 服務的本機環境，以供開發之用。 使用本文中的秘訣，協助您針對安裝或使用 Azure Cosmos 模擬器時所遇到的問題進行疑難排解。 

如果您已安裝新版本的模擬器但發生了錯誤，請務必重設您的資料。 您可以用滑鼠右鍵按一下系統匣上的 Azure Cosmos 模擬器圖示，然後按一下 [重設資料 ...]，以重設資料。 如果無法修正錯誤，您可以卸載模擬器和任何舊版模擬器（如果找到的話），移除 *C:\Program files\Azure Cosmos DB 模擬器* 目錄，然後重新安裝模擬器。 如需相關指示，請參閱[解除安裝本機模擬器](local-emulator.md#uninstall)。 或者，如果重設資料沒有作用，請流覽至 `%LOCALAPPDATA%\CosmosDBEmulator` 位置並刪除資料夾。

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>針對損毀的 windows 效能計數器進行疑難排解

* 如果 Azure Cosmos 模擬器當機，請從資料夾收集傾印檔案、壓縮檔案， `%LOCALAPPDATA%\CrashDumps` 然後從 [Azure 入口網站](https://portal.azure.com)開啟支援票證。

* 如果您在 `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` 中遇到當機，這可能是效能計數器處於損毀狀態的徵兆。 通常從系統管理員的命令提示字元中執行下列命令，即可修正此問題：

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>疑難排解連線問題

* 如果發生連線問題，請[收集追蹤檔案](#trace-files)並加以壓縮，然後在 [Azure 入口網站](https://portal.azure.com)中開啟支援票證。

* 如果您收到**服務無法使用**訊息，模擬器可能無法初始化網路堆疊。 由於 Pulse 安全用戶端或 Juniper 網路用戶端的網路篩選驅動程式可能會造成問題，因此請檢查是否已安裝這些驅動程式。 解除安裝協力廠商網路篩選驅動程式通常便會修正問題。 或者，使用 /DisableRIO 來啟動模擬器，將模擬器網路通訊切換為一般 Winsock。 

* 如果您遇到「 **禁止」，則會使用傳輸通訊協定或加密中禁止的加密來發出「訊息」： "要求。檢查帳戶 SSL/TLS 的最小允許通訊協定設定 ...** 」連線問題，這可能是 OS 中的全域變更所造成的 (例如 Insider Preview 組建 20170) ，或啟用 TLS 1.3 作為預設值的瀏覽器設定。 使用 SDK 對 Cosmos 模擬器執行要求時，可能會發生類似的錯誤，例如 **Microsoft.Azure.Documents.DocumentClientException：使用傳輸通訊協定或加密中的禁止加密提出要求。檢查帳戶 SSL/TLS 允許的最小通訊協定設定**。 就目前而言，這是的預期行為，因為 Cosmos 模擬器僅接受並適用 TLS 1.2 通訊協定。 建議的解決辦法是將設定和預設值變更為 TLS 1.2;例如，在 [IIS 管理員] 中，流覽至 [網站]-> [預設的網站]，並找出埠8081的「網站系結」，並加以編輯以停用 TLS 1.3。 您可以透過 [設定] 選項對網頁瀏覽器執行類似的作業。

* 當模擬器執行時，如果您的電腦進入睡眠模式或執行任何作業系統更新，您應該會看見**服務目前無法使用**的訊息。 以滑鼠右鍵按一下視窗通知匣上出現的圖示，然後選取 [重設資料]，來重設模擬器的資料。

## <a name="collect-trace-files"></a><a id="trace-files"></a>收集追蹤檔案

若要收集偵錯追蹤，請從系統管理命令提示字元執行下列命令︰

1. 流覽至安裝模擬器的路徑：

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. 關閉模擬器並監看系統匣，確定程式已關閉。 可能需要一分鐘的時間才能完成。 您也可以在 Azure Cosmos 模擬器使用者 **介面中選取** [結束]。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. 使用下列命令開始記錄：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. 啟動模擬器

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. 重現問題。 如果資料 explorer 無法運作，您只需要等待瀏覽器開啟幾秒鐘的時間來攔截錯誤。

1. 使用下列命令停止記錄：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. 流覽至 `%ProgramFiles%\Azure Cosmos DB Emulator` [路徑]，然後尋找 *docdbemulator_000001 的 etl* 檔。

1. 在 [Azure 入口網站](https://portal.azure.com)中開啟支援票證，並納入 .etl 檔案與重現步驟。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用本機模擬器來偵測問題。 您現在可以繼續進行下一篇文章：

* [匯出 Azure Cosmos 模擬器憑證以與 JAVA、Python 和 Node.js apps 搭配使用](local-emulator-export-ssl-certificates.md)
* [使用命令列參數和 PowerShell 命令來控制模擬器](emulator-command-line-parameters.md)
