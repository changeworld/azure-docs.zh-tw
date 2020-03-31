---
title: 本機快取
description: 瞭解本機快取在 Azure 應用服務中的工作方式，以及如何啟用、調整和查詢應用本機快取的狀態。
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227878"
---
# <a name="azure-app-service-local-cache-overview"></a>Azure App Service 本機快取概觀

> [!NOTE]
> 功能應用或容器化應用服務應用不支援本機快取，例如[在 Windows 容器](app-service-web-get-started-windows-container.md)中或在 Linux[上的應用程式服務上](containers/app-service-linux-intro.md)。


Azure App Service 的內容儲存在 Azure 儲存體中，並且會持久顯示為內容共用。 這項設計的目的是為了要與各種應用程式搭配使用，而且其具有下列特性︰  

* 內容會由應用程式的多個虛擬機器 (VM) 執行個體共用。
* 內容具有持久性，且可透過執行應用程式來修改。
* 記錄檔和診斷資料檔案會在相同的共用內容資料夾底下提供使用。
* 發佈新內容時會直接更新內容資料夾。 您可以通過 SCM 網站和正在運行的應用立即查看相同的內容（通常某些技術（如ASP.NET會在某些檔更改上啟動應用重新開機以獲取最新內容）。

雖然許多應用程式會使用上述其中一項功能或所有功能，但某些應用程式只需要可從中執行的內容存放區，而且此存放區具備高效能、唯讀和高可用性。 這些應用程式可以受益於特定本機快取的 VM 執行個體。

Azure App Service 本機快取功能可讓您以 Web 角色檢視您的內容。 這個內容是在網站啟動時，以非同步方式建立之儲存體內容的 write-but-discard 快取。 當快取已準備就緒時，網站會切換為執行快取的內容。 在本機快取執行的應用程式具有下列優點︰

* 它們不受存取 Azure 儲存體上的內容時所發生的延遲影響。
* 它們不受提供內容共用之伺服器所進行的計劃性升級或非計劃性停機，以及 Azure 儲存體的任何其他中斷所影響。
* 應用程式比較不會因為儲存體共用變更而重新啟動。

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>本機快取對 App Service 的行為所造成的影響
* _D:\home_ 會指向應用程式啟動時，在 VM 執行個體上建立的本機快取。 _D：\local_繼續指向臨時 VM 特定的存儲。
* 本機快取中包含共用內容存放區上 _/site_ 和 _/siteextensions_ 資料夾的一次性副本，分別位在 _D:\home\site_ 和 _D:\home\siteextensions_。 檔案會在應用程式啟動時複製到本機快取。 針對每個應用程式，兩個資料夾的大小限制預設為 300 MB，但最多可以增加至 2 GB。 如果複製的檔超過本機快取的大小，應用服務會靜默忽略本機快取並從遠端檔共用讀取。
* 本機快取具有讀寫屬性。 不過，當應用程式移動虛擬機器或重新啟動時，將會捨棄任何修改。 因此，請勿針對會在內容存放區中儲存關鍵任務資料的應用程式使用本機快取。
* _D:\home\LogFiles_ 和 _D:\home\Data_ 中包含記錄檔和應用程式資料。 這兩個子資料夾會儲存在本機上的 VM 執行個體中，並定期複製到共用內容存放區。 應用程式可以藉由將記錄檔和資料寫入這些資料夾來加以保存。 不過，複製到共用內容存放區已是最佳方式，記錄檔和資料還是可能會因為 VM 執行個體突然當機而遺失。
* [記錄串流](troubleshoot-diagnostic-logs.md#stream-logs)會受到此最佳複製方式的影響。 您可能會看到串流處理的記錄中有最多一分鐘的延遲。
* 在共用內容存放區中，針對使用本機快取的應用程式，其 _LogFiles_ 和 _Data_ 資料夾的資料夾結構有所變更。 這些資料夾現在有子資料夾，且這些子資料夾會遵循「唯一識別碼」+ 時間戳記的命名模式。 每個子資料夾都對應到將要用來執行或已用來執行應用程式的 VM 執行個體。
* _D:\home_ 中的其他資料夾會保留在本機快取中，而且不會複製到共用內容存放區。
* 透過任何支援方法執行的應用程式部署會直接發佈到永久的共用內容存放區中。 若要重新整理本機快取中的 _D:\home\site_ 和 _D:\home\siteextensions_ 資料夾，必須重新啟動應用程式。 為了讓整個生命週期順暢前進，請參閱本文稍後的資訊。
* SCM 網站的預設內容檢視將仍是共用內容存放區。

## <a name="enable-local-cache-in-app-service"></a>在 App Service 中啟用本機快取
您可以使用保留的應用程式設定的組合，以設定本機快取。 這些應用程式設定可以使用下列方法來設定：

* [Azure 門戶](#Configure-Local-Cache-Portal)
* [Azure 資源管理器](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>使用 Azure 入口網站設定本機快取
<a name="Configure-Local-Cache-Portal"></a>

您可以使用下列應用程式設定來針對每個 Web 應用程式啟用本機快取︰ `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure 入口網站應用程式設定︰本機快取](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 設定本機快取
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>變更本機快取中的大小設定
根據預設，本機快取大小是 **300 MB**。 此大小包括複製自內容存放區的 /site 和 /siteextensions 資料夾，以及任何在本機建立之記錄和資料的資料夾。 若要增加此限制，請使用應用程式設定 `WEBSITE_LOCAL_CACHE_SIZEINMB`。 每個應用程式的大小最多可以增加為 **2 GB** (2000 MB)。

## <a name="best-practices-for-using-app-service-local-cache"></a>使用 App Service 本機快取的最佳作法
建議您搭配 [預備環境](../app-service/deploy-staging-slots.md) 功能使用本機快取。

* 將「黏性」** 應用程式設定 `WEBSITE_LOCAL_CACHE_OPTION` 與值 `Always` 新增至您的**生產環境**位置。 如果您要使用 `WEBSITE_LOCAL_CACHE_SIZEINMB`，也將它做為黏性設定新增至您的生產環境位置。
* 建立 **預備環境** 位置，並發佈至您的預備環境位置。 如果您獲得生產環境位置之「本機快取」提供的好處，便通常不會將預備環境位置設定為使用本機快取以在預備環境獲得順暢的「建置、部署、測試」生命週期。
* 針對預備位置測試您的網站。  
* 準備好後，請在您的「預備環境」和「生產環境」位置之間發出 [交換作業](../app-service/deploy-staging-slots.md#Swap) 。  
* 黏性設定包含位置的名稱和黏性。 因此，當預備環境位置交換到生產環境時，它會繼承本機快取應用程式設定。 新交換的生產環境位置會在幾分鐘後針對「本機快取」執行，並會在交換後的位置準備就緒時一併就緒。 因此，完成位置交換時，將會針對本機快取執行您的生產環境位置。

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>如何知道「本機快取」是否適用於我的應用程式？
如果您的應用程式需要高效能、可靠的內容存放區，且不使用內容存放區在執行階段時寫入重要資料並且大小總計小於 2 GB，那麼答案就是肯定的！ 若要取得您 /site 和 /siteextensions 資料夾的大小總計，您可以使用網站擴充功能「Azure Web Apps 磁碟使用量」。

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>如何知道我的網站是否已切換成使用本機快取？
如果搭配預備環境使用「本機快取」功能，在本機快取準備就緒之前將無法完成交換作業。 若要檢查您的網站是否正在執行本機快取，您可以檢查工作者處理序環境變數 `WEBSITE_LOCALCACHE_READY`。 使用 [背景工作角色處理序環境變數](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) 頁面中的指示，存取多個執行個體上的工作者處理序環境變數。  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>我剛剛發佈了新的變更，但我的應用程式似乎沒有變更。 原因為何？
如果您的應用程式使用「本機快取」，則您需要重新啟動您的網站，以取得最新的變更。 不想對生產網站發佈變更嗎？ 請參閱前面的最佳作法一節中的位置選項。

### <a name="where-are-my-logs"></a>我的記錄在哪裡？
使用本機快取，您的記錄和資料的資料夾看起來會有點不同。 不過，子資料夾的結構會保持相同，不同之處在於這些子資料夾會位在格式為「唯一 VM 識別碼」+ 時間戳記的子資料夾底下。

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>我已啟用「本機快取」，但我的應用程式仍然重新啟動。 這是為什麼？ 我以為「本機快取」有助於頻繁的應用程式重新啟動。
「本機快取」確實有助於防止儲存體相關應用程式重新啟動。 不過，您的應用程式仍有可能在計劃性 VM 基礎結構升級期間重新啟動。 整體而言，啟用「本機快取」後應該比較不會發生應用程式重新啟動。

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>「本機快取」會排除所有目錄而不會複製到更快的本機磁碟機嗎？
在複製儲存體內容的步驟中，將會排除名為儲存機制的任何資料夾。 當您的網站內容可能包含應用程式的日常作業不需要的原始檔控制儲存機制時，這會有幫助。 
