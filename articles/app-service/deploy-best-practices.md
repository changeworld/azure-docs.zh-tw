---
title: 部署最佳做法
description: 瞭解部署到 Azure 應用服務的關鍵機制。 查找特定于語言的建議和其他注意事項。
keywords: Azure 應用服務、Web 應用、部署、部署、管道、生成
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750476"
---
# <a name="deployment-best-practices"></a>部署最佳實踐

每個開發團隊都有獨特的要求，使在任何雲服務上實現高效的部署管道都很困難。 本文介紹了部署到應用服務的三個主要元件：部署源、生成管道和部署機制。 本文還介紹了特定語言堆疊的一些最佳實踐和提示。

## <a name="deployment-components"></a>部署元件

### <a name="deployment-source"></a>部署源

部署源是應用程式代碼的位置。 對於生產應用，部署源通常是由版本控制軟體（如[GitHub、BitBucket 或 Azure 存儲庫](deploy-continuous-deployment.md)）託管的存儲庫。 對於開發和測試方案，部署源可能是[本地電腦上的專案](deploy-local-git.md)。 應用服務還支援[OneDrive 和 Dropbox 資料夾](deploy-content-sync.md)作為部署源。 雖然雲資料夾可以方便地開始使用應用服務，但通常不建議將此源用於企業級生產應用程式。 

### <a name="build-pipeline"></a>組建管線

確定部署源後，下一步是選擇生成管道。 生成管道從部署源讀取原始程式碼，並執行一系列步驟（如編譯代碼、對 HTML 和 JavaScript 進行簡單化、運行測試和打包元件），以使應用程式處於可運行狀態。 生成管道執行的特定命令取決於您的語言堆疊。 這些操作可以在生成伺服器上執行（如 Azure 管道），也可以在本地執行。

### <a name="deployment-mechanism"></a>部署機制

部署機制是用於將內置應用程式放入 Web 應用的 */home/site/wwwroot*目錄中的操作。 */wwwroot*目錄是 Web 應用的所有實例共用的裝載存儲位置。 當部署機制將應用程式放入此目錄中時，實例會收到同步新檔的通知。 應用服務支援以下部署機制：

- Kudu 終結點[：Kudu](https://github.com/projectkudu/kudu/wiki)是開源開發人員生產力工具，在 Windows 應用服務中作為單獨的進程運行，並作為 Linux 應用服務中的第二個容器運行。 庫杜處理連續部署並提供用於部署的 HTTP 終結點，如 zipdeploy。
- FTP 和 WebDeploy：使用[您的網站或使用者憑據](deploy-configure-credentials.md)，您可以通過[FTP](deploy-ftp.md)或 WebDeploy 上傳檔。 這些機制不通過庫杜。  

部署工具（如 Azure 管道、Jenkins 和編輯器外掛程式）使用這些部署機制之一。

## <a name="language-specific-considerations"></a>特定于語言的注意事項

### <a name="java"></a>Java

使用 Kudu [zipdeploy/API](deploy-zip.md)部署 JAR 應用程式，以及 WAR 應用程式[的戰爭部署/](deploy-zip.md#deploy-war-file) 如果使用 Jenkins，則可以在部署階段直接使用這些 API。 如需詳細資訊，請參閱 [本篇文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>節點

預設情況下，Kudu 執行 Node 應用程式 （）`npm install`的生成步驟。 如果使用生成服務（如 Azure DevOps），則不需要 Kudu 生成。 要禁用 Kudu 生成，請創建一個`SCM_DO_BUILD_DURING_DEPLOYMENT`應用設置，其值`false`為 。

### <a name="net"></a>.NET 

預設情況下，Kudu 執行 .Net 應用程式的生成步驟 （`dotnet build`。 如果使用生成服務（如 Azure DevOps），則不需要 Kudu 生成。 要禁用 Kudu 生成，請創建一個`SCM_DO_BUILD_DURING_DEPLOYMENT`應用設置，其值`false`為 。

## <a name="other-deployment-considerations"></a>其他部署注意事項

### <a name="use-deployment-slots"></a>使用部署槽

盡可能在部署新的生產生成時使用[部署槽](deploy-staging-slots.md)。 使用標準應用服務方案層或更高級別時，可以將應用部署到暫存環境、驗證更改並執行煙霧測試。 準備就緒後，可以交換暫存和生產槽。 交換操作預熱必要的輔助角色實例，以匹配您的生產規模，從而消除停機時間。 

### <a name="local-cache"></a>本機快取

Azure App Service 的內容儲存在 Azure 儲存體中，並且會持久顯示為內容共用。 但是，某些應用只需要一個高性能的、唯讀的內容存儲，它們就可以以高可用性運行。 這些應用程式可以從使用[本機快取](overview-local-cache.md)中獲益。 不建議對內容管理網站（如 WordPress）進行本機快取。

始終將本機快取與[部署插槽](deploy-staging-slots.md)結合使用，以防止停機。 有關將這些功能結合使用的資訊，請參閱[本節](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或記憶體

如果應用服務方案使用了超過 90% 的可用 CPU 或記憶體，則基礎虛擬機器在處理部署時可能遇到問題。 發生這種情況時，請臨時向上擴展實例計數以執行部署。 部署完成後，可以將實例計數返回到其上一個值。

有關最佳實踐的詳細資訊，請訪問[應用服務診斷](https://docs.microsoft.com/azure/app-service/overview-diagnostics)，瞭解特定于您的資源的可操作最佳做法。

- 在[Azure 門戶](https://portal.azure.com)中導航到 Web 應用。
- 按一下 **"診斷並解決**左側導航中的問題"，該導航將打開應用服務診斷。
- 選擇**最佳實踐**主頁磁貼。
- 按一下 **"最佳**配置&可用性的最佳做法"或 **"最佳配置的最佳做法**"，查看應用對這些最佳實踐的目前狀態。

您還可以使用此連結直接打開資源的應用服務診斷： `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`。
