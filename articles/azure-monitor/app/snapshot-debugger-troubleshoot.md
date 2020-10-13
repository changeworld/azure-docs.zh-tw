---
title: 針對 Azure 應用程式 Insights 快照偵錯工具進行疑難排解
description: 本文提供疑難排解步驟和資訊，協助開發人員在啟用或使用 Application Insights 快照偵錯工具時發生問題。
ms.topic: conceptual
author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bb2ac221169cea84205d087cbe0aadfd035d22db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760507"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> 針對啟用 Application Insights 快照偵錯工具或查看快照集的問題進行疑難排解
如果您已為應用程式啟用 Application Insights 快照偵錯工具，但是沒有看到例外狀況的快照集，您可以使用這些指示來進行疑難排解。 不會產生快照集有許多不同的原因。 您可以執行快照集健康情況檢查，以找出一些可能的常見原因。

## <a name="use-the-snapshot-health-check"></a>使用快照集健康情況檢查
一些常見的問題會導致不會顯示開啟偵錯快照集。 例如，使用過期的快照集收集器；達到每日上傳限制；或者快照集可能會花很長的時間來上傳。 請使用快照集健康情況檢查，針對常見的問題進行疑難排解。

端對端追蹤檢視的例外狀況窗格中有一個連結，可帶領您前往快照集健康情況檢查。

![輸入快照集健康情況檢查](./media/snapshot-debugger/enter-snapshot-health-check.png)

互動式的聊天型介面可尋找常見的問題，並引導您修正它們。

![健康情況檢查](./media/snapshot-debugger/healthcheck.png)

如果這樣無法解決問題，則請參閱下列手動疑難排解步驟。

## <a name="verify-the-instrumentation-key"></a>驗證檢測金鑰

請確定您在已發佈的應用程式中使用正確的檢測金鑰。 通常，會從 ApplicationInsights.config 檔案中讀取檢測金鑰。 請確認此值與您在入口網站中看到之 Application Insights 資源的檢測金鑰相同。

## <a name="check-ssl-client-settings-aspnet"></a><a id="SSL"></a>檢查 (ASP.NET) 的 SSL 用戶端設定

如果您的 ASP.NET 應用程式裝載于 Azure App Service 或虛擬機器上的 IIS 中，您的應用程式可能會因為遺失 SSL 安全性通訊協定而無法連線到快照偵錯工具服務。
[快照偵錯工具端點需要 TLS 1.2 版](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json)。 SSL 安全性通訊協定集合是 web.config 的 [HTTPRuntime] targetFramework 值所啟用的其中一組功能。如果 HTTPRuntime targetFramework 為4.5.2 或更低，則預設不會包含 TLS 1.2。

> [!NOTE]
> HttpRuntime targetFramework 值獨立于建立應用程式時所使用的目標架構。

若要檢查設定，請開啟您的 web.config 檔案，然後尋找 [system.object] 區段。 確定的 `targetFramework` 設為 `httpRuntime` 4.6 或更新版本。

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> 修改 HTTPRuntime targetFramework 值會變更套用至應用程式的執行時間特性，而且可能會造成其他的微妙行為變更。 進行此變更之後，請務必徹底測試您的應用程式。 如需相容性變更的完整清單，請參閱 https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> 如果 targetFramework 為4.7 或更新版本，則 Windows 會決定可用的通訊協定。 在 Azure App Service 中，可以使用 TLS 1.2。 但是，如果您使用自己的虛擬機器，可能需要在作業系統中啟用 TLS 1.2。

## <a name="preview-versions-of-net-core"></a>.NET Core 的預覽版本
如果應用程式使用 .NET Core 的預覽版本，並透過入口網站中的 [ [Application Insights] 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) 啟用快照偵錯工具，則快照偵錯工具可能無法啟動。 請先依照 [針對其他環境啟用快照偵錯工具](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 的指示，先將 [ApplicationInsights microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件 ***與應用程式搭配使用，再*** 透過 [Application Insights 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)啟用。


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>升級至最新版本的 NuGet 套件

如果透過 [入口網站中的 [Application Insights] 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)啟用快照偵錯工具，則您的應用程式應該已在執行最新的 NuGet 套件。 如果已藉由包含 [ApplicationInsights Microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nuget 套件來啟用快照偵錯工具，請使用 Visual Studio 的 nuget 封裝管理員，以確定您使用的是最新版本的 ApplicationInsights. microsoft.applicationinsights.snapshotcollector。

## <a name="check-the-uploader-logs"></a>請檢查上傳程式記錄

建立快照集之後，磁碟上會建立小型傾印檔案 (.dmp)。 個別的上傳程式處理序會建立該小型傾印檔案，並將它 (以及任何相關聯的 PDB) 上傳至 Application Insights 快照集偵錯工具儲存體。 成功上傳小型傾印之後，它就會從磁碟中刪除。 上傳程式處理序的記錄檔會保留在磁碟上。 在 App Service 環境中，您可以在 `D:\Home\LogFiles` 中找到這些記錄。 使用 App Service 的 Kudu 管理網站來尋找這些記錄檔。

1. 在 Azure 入口網站中開啟您的 App Service 應用程式。
2. 按一下 [進階工具]****，或搜尋 **Kudu**。
3. 按一下 **[移至]**。
4. 在 [偵錯主控台]**** 下拉式清單方塊中，選取 [CMD]****。
5. 按一下 [LogFiles]****。

您應會看到至少有一個檔案的名稱開頭為 `Uploader_` 或 `SnapshotUploader_` 且副檔名為 `.log`。 按一下適當的圖示，以下載任何記錄檔，或在瀏覽器中開啟它們。
檔案名稱包含可識別 App Service 執行個體的唯一尾碼。 如果 App Service 執行個體裝載於一部以上的電腦，每部電腦會有個別的記錄檔。 當上傳程式偵測到新的小型傾印檔案時，該檔案會記錄在記錄檔中。 以下是成功快照集和上傳的範例︰

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> 上述範例來自於 1.2.0 版的 Microsoft.ApplicationInsights.SnapshotCollector NuGet 套件。 在較舊的版本中，上傳程式程序稱為 `MinidumpUploader.exe`，且記錄較不詳細。

在上述範例中，檢測金鑰為 `c12a605e73c44346a984e00000000000`。 這個值應該符合您應用程式的檢測金鑰。
小型傾印會與識別碼為 `139e411a23934dc0b9ea08a626db16c5` 的快照集相關聯。 您稍後可以使用這個識別碼，在 Application Insights Analytics 中找出相關聯的例外狀況遙測。

上載程式約每隔 15 分鐘掃描一次新的 PDB。 以下為範例：

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

若為「未」__ 裝載於 App Service 中的應用程式，上傳程式記錄位於與小型傾印相同的資料夾中：`%TEMP%\Dumps\<ikey>` (其中 `<ikey>` 是您的檢測金鑰)。

## <a name="troubleshooting-cloud-services"></a>針對雲端服務進行疑難排解
針對雲端服務中的角色，預設暫存資料夾可能太小，無法保存小型傾印檔案，進而導致遺失快照集。
所需的空間取決於您應用程式的總工作集以及並行快照集數目。
32 位元 ASP.NET Web 角色的工作集一般介於 200 MB 與 500 MB 之間。
允許至少兩個並行快照集。
例如，如果您的應用程式使用 1 GB 的總工作集，則應確定至少有 2 GB 的磁碟空間可儲存快照集。
請遵循下列步驟，以使用快照集的專用本機資源來設定您的雲端服務角色。

1. 編輯雲端服務定義 (.csdef) 檔案，以將新的本機資源新增至雲端服務。 下列範例定義稱為 `SnapshotStore` 且大小為 5 GB 的資源。
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. 修改角色的啟動程式碼，新增指向 `SnapshotStore` 本機資源的環境變數。 對於背景工作角色，程式碼應新增至您角色的 `OnStart` 方法：
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   對於 Web 角色 (ASP.NET)，程式碼應新增至您 Web 應用程式的 `Application_Start` 方法：
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. 更新您角色的 ApplicationInsights.config 檔案，以覆寫 `SnapshotCollector` 所使用的暫存資料夾位置
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>覆寫陰影複製資料夾

快照集收集器啟動時，它會嘗試在磁碟上尋找適合用來執行快照集上傳程式程序的資料夾。 選擇的資料夾稱為陰影複製資料夾。

快照集收集器會檢查幾個已知的位置，確定它有權複製快照集上傳程式二進位檔。 使用的環境變數如下：
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

如果找不到適合的資料夾，快照集收集器會報告錯誤，指出「找不到適合的陰影複製資料夾。」__

如果複製失敗，快照集收集器會報告 `ShadowCopyFailed` 錯誤。

如果上傳程式無法啟動，快照集收集器會報告 `UploaderCannotStartFromShadowCopy` 錯誤。 訊息內文通常會包含 `System.UnauthorizedAccessException`。 之所以發生此錯誤，通常是因為應用程式執行於權限降低的帳戶下。 該帳戶有權寫入陰影複製資料夾，但沒有執行程式碼的權限。

這些錯誤通常是在啟動期間發生的，因此通常會尾隨 `ExceptionDuringConnect` 錯誤，指出「上傳程式無法啟動。」__

若要解決這些錯誤，您可以透過 `ShadowCopyFolder` 組態選項以手動方式指定陰影複製資料夾。 例如，使用 ApplicationInsights.config：

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

或者，如果您搭配使用 appsettings.json 與 .NET Core 應用程式：

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>使用 Application Insights 搜尋來尋找快照集例外狀況的

建立快照集後，擲回中的例外狀況會以快照集識別碼標記。 向 Application Insights 回報例外狀況遙測後，該快照集識別碼就會包含為自訂屬性。 使用 Application Insights 中的 [搜尋]**** 刀鋒視窗，您可以找到具有 `ai.snapshot.id` 自訂屬性的所有遙測。

1. 在 Azure 入口網站中瀏覽至您的 Application Insights 資源。
2. 按一下 **[搜尋]** 。
3. 在 [搜尋] 文字方塊中輸入 `ai.snapshot.id`，然後按 Enter 鍵。

![在入口網站中使用快照集識別碼搜尋遙測](./media/snapshot-debugger/search-snapshot-portal.png)

如果此搜尋未傳回任何結果，則不會針對所選時間範圍中您的應用程式向 Application Insights 回報任何快照集。

若要搜尋從上傳程式記錄中的特定快照集識別碼，請在 [搜尋] 方塊中輸入該識別碼。 如果您找不到已上傳快照集的遙測，請遵循下列步驟：

1. 請驗證檢測金鑰，仔細檢查您查看的是正確的 Application Insights 資源。

2. 使用上傳程式記錄中的時間戳記，調整搜尋的時間範圍篩選條件以涵蓋該時間範圍。

如果仍未看到具有該快照集識別碼的例外狀況，則未向 Application Insights 回報此例外狀況遙測。 如果您的應用程式在採用快照集之後，但回報例外狀況遙測之前損毀，可能會發生這種情況。 在此情況下，檢查 `Diagnose and solve problems` 之下的 App Service 記錄，查看是否發生非預期的重新啟動或未處理的例外狀況。

## <a name="edit-network-proxy-or-firewall-rules"></a>編輯網路 Proxy 或防火牆規則

如果應用程式透過 Proxy 或防火牆連線至網際網路，您可能需要編輯規則以允許應用程式與快照集偵錯工具服務進行通訊。 快照偵錯工具所使用的 Ip 會包含在 Azure 監視器服務標記中。
