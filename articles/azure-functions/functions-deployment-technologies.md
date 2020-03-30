---
title: Azure 函數中的部署技術
description: 瞭解將代碼部署到 Azure 函數的不同方法。
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277124"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure 函數中的部署技術

可以使用幾種不同的技術將 Azure 函數專案代碼部署到 Azure。 本文提供了這些技術的詳盡清單，介紹了哪些技術可用於哪些功能類型，解釋了使用每種方法時會發生什麼情況，並針對在各種方案中使用的最佳方法提供了建議. 支援部署到 Azure 函數的各種工具會根據其上下文調整到正確的技術。 通常，zip 部署是 Azure 函數的推薦部署技術。

## <a name="deployment-technology-availability"></a>部署技術可用性

Azure 函數支援跨平臺本地開發和在 Windows 和 Linux 上的託管。 目前，有三個託管計畫：

+ [耗用量](functions-scale.md#consumption-plan)
+ [溢價](functions-scale.md#premium-plan)
+ [專用（應用服務）](functions-scale.md#app-service-plan)

每個計畫都有不同的行為。 並非所有部署技術都可用於 Azure 函數的每個風格。 下圖顯示了作業系統和託管計畫的每個組合都支援哪些部署技術：

| 部署技術 | 視窗消耗 | 視窗高級版 | 專用視窗  | Linux 消費 | Linux 高級版 | Linux 專用 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 外部包 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip 部署 |✔|✔|✔|✔|✔|✔|
| Docker 容器 | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| 原始檔控制 |✔|✔|✔| |✔|✔|
| 本地 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 雲同步<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| 門戶編輯 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup>需要[手動觸發器同步的部署](#trigger-syncing)技術。  
<sup>2</sup>門戶編輯僅針對使用高級和專用計畫在 Linux 上函數的 HTTP 和計時器觸發器啟用。

## <a name="key-concepts"></a>重要概念

對於瞭解部署在 Azure 函數中的工作方式，一些關鍵概念至關重要。

### <a name="trigger-syncing"></a>觸發同步

更改任何觸發器時，功能基礎結構必須瞭解更改。 對於許多部署技術，自動同步發生。 但是，在某些情況下，您必須手動同步觸發器。 通過引用外部包 URL、本地 Git、雲同步或 FTP 來部署更新時，必須手動同步觸發器。 您可以通過三種方式之一同步觸發器：

* 在 Azure 門戶中重新開機功能應用
* 使用[主金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)發送 HTTP `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` POST 請求。
* 向 發送 HTTP `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`POST 請求。 將預留位置替換為訂閱 ID、資源組名稱和函數應用的名稱。

### <a name="remote-build"></a>遠端生成

Azure 函數可以根據在 zip 部署後收到的代碼自動執行生成。 這些生成的行為略有不同，具體取決於你的應用是在 Windows 上運行還是在 Linux 上運行。 當應用以前已設置為在["從包運行"](run-functions-from-deployment-package.md)模式下運行時，不會執行遠端生成。 要瞭解如何使用遠端生成，請使用導航到[zip 部署](#zip-deploy)。

> [!NOTE]
> 如果您在遠端生成時遇到問題，可能是因為應用是在功能可用之前創建的（2019 年 8 月 1 日）。 請嘗試創建新的函數應用，或運行`az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>`以更新函數應用。 此命令可能需要兩次嘗試才能成功。

#### <a name="remote-build-on-windows"></a>Windows 上的遠端生成

在 Windows 上運行的所有功能應用都有一個小型管理應用，即 SCM（或[Kudu](https://github.com/projectkudu/kudu)） 網站。 此網站處理 Azure 函數的大部分部署和生成邏輯。

將應用部署到 Windows 時，將運行特定于語言的命令，`dotnet restore`如 （C#） 或`npm install`（JavaScript）。

#### <a name="remote-build-on-linux"></a>Linux 上的遠端構建

要在 Linux 上啟用遠端構建，必須設置以下[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)：

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

預設情況下[，Azure 函數核心工具和](functions-run-local.md)[視覺化工作室代碼的 Azure 函數擴展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)在部署到 Linux 時執行遠端生成。 因此，這兩個工具都會在 Azure 中自動為您創建這些設置。 

當應用程式在 Linux 上遠端構建時，它們[將從部署包運行](run-functions-from-deployment-package.md)。 

##### <a name="consumption-plan"></a>取用方案

在"消費"計畫中運行的 Linux 函數應用沒有 SCM/Kudu 網站，這限制了部署選項。 但是，在"消費"計畫中運行的 Linux 上的函數應用確實支援遠端生成。

##### <a name="dedicated-and-premium-plans"></a>專用和高級計畫

在 Linux 上運行的[專用（應用服務）計畫和](functions-scale.md#app-service-plan)[高級計畫](functions-scale.md#premium-plan)中的功能應用也有有限的 SCM/Kudu 網站。

## <a name="deployment-technology-details"></a>部署技術詳細資訊

以下部署方法在 Azure 函數中可用。

### <a name="external-package-url"></a>外部包 URL

您可以使用外部包 URL 引用包含函數應用的遠端包 （.zip） 檔。 該檔從提供的 URL 下載，應用在["從包運行"](run-functions-from-deployment-package.md)模式下運行。

>__如何使用它：__ 添加到`WEBSITE_RUN_FROM_PACKAGE`應用程式設定。 此設置的值應為 URL（要運行的特定包檔的位置）。 您可以在[門戶中](functions-how-to-use-azure-function-app-settings.md#settings)或使用[Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)添加設置。 
>
>如果使用 Azure Blob 存儲，請使用具有[共用訪問簽名 （SAS）](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)的專用容器，使函數可以訪問包。 每當應用程式重新開機時，它都獲取內容的副本。 您的引用必須在應用程式的存留期內有效。

>__何時使用它：__ 如果使用者不希望[遠端生成](#remote-build)發生，則外部包 URL 是使用計畫中在 Linux 上運行的 Azure 函數的唯一受支援的部署方法。 更新函數應用引用的包檔時，必須[手動同步觸發器](#trigger-syncing)，告訴 Azure 應用程式已更改。

### <a name="zip-deploy"></a>Zip 部署

使用 zip 部署將包含函數應用的 .ZIP 檔案推送到 Azure。 或者，可以將應用設置為[從包開始運行](run-functions-from-deployment-package.md)，或者指定發生[遠端生成](#remote-build)。

>__如何使用它：__ 使用您最喜愛的用戶端工具進行部署：[視覺化工作室代碼](functions-develop-vs-code.md#publish-to-azure)、[視覺化工作室](functions-develop-vs.md#publish-to-azure)，或使用[Azure 函數核心工具](functions-run-local.md#project-file-deployment)從命令列進行部署。 預設情況下，這些工具使用 zip 部署並從[包運行](run-functions-from-deployment-package.md)。 核心工具和視覺化工作室代碼擴展在部署到 Linux 時都啟用[遠端構建](#remote-build)。 要手動將 .ZIP 檔案部署到函數應用，請按照[.ZIP 檔案或 URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)中的說明進行操作。

>使用 zip 部署進行部署時，可以將應用設置為[從包運行](run-functions-from-deployment-package.md)。 要從包運行，請將`WEBSITE_RUN_FROM_PACKAGE`應用程式設定值設置為`1`。 我們建議使用 zip 部署。 它為應用程式帶來更快的載入時間，並且它是 VS 代碼、視覺化工作室和 Azure CLI 的預設值。 

>__何時使用它：__ Zip 部署是 Azure 函數的推薦部署技術。

### <a name="docker-container"></a>Docker 容器

您可以部署包含函數應用的 Linux 容器映射。

>__如何使用它：__ 在高級或專用計畫中創建 Linux 函數應用，並指定要從哪個容器映射運行。 您可以使用兩種方式執行此動作：
>
>* 在 Azure 門戶中的 Azure 應用服務方案中創建 Linux 函數應用。 對於**發佈**，請選擇**Docker 映射**，然後配置容器。 輸入圖像託管的位置。
>* 使用 Azure CLI 在應用服務方案中創建 Linux 函數應用。 要瞭解如何使用自訂[映射在 Linux 上創建函數](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)。
>
>要使用自訂容器（[在 Azure 函數核心工具](functions-run-local.md)中）部署到現有應用，請使用[`func deploy`](functions-run-local.md#publish)命令。

>__何時使用它：__ 當您需要對運行函數應用的 Linux 環境進行更多控制時，請使用 Docker 容器選項。 此部署機制僅適用于在 Linux 上運行的函數。

### <a name="web-deploy-msdeploy"></a>Web 部署 （MSDeploy）

Web 部署包並將 Windows 應用程式部署到任何 IIS 伺服器，包括在 Azure 中的 Windows 上運行的函數應用程式。

>__如何使用它：__ 使用[視覺化工作室工具進行 Azure 函數](functions-create-your-first-function-visual-studio.md)。 清除"**從包運行檔（推薦）"** 核取方塊。
>
>您還可以下載[Web 部署 3.6](https://www.iis.net/downloads/microsoft/web-deploy) `MSDeploy.exe`並直接呼叫。

>__何時使用它：__ Web 部署受支援且沒有問題，但首選機制是[啟用"從包運行"的 zip 部署](#zip-deploy)。 要瞭解更多資訊，請參閱[視覺化工作室開發指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>原始檔控制

使用原始程式碼管理將函數應用連接到 Git 存儲庫。 對該存儲庫中的代碼的更新將觸發部署。 有關詳細資訊，請參閱[庫杜維琪](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__如何使用它：__ 使用門戶功能區域中的部署中心從原始程式碼管理設置發佈。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。

>__何時使用它：__ 對於協作處理其功能應用的團隊來說，使用原始程式碼管理是最佳做法。 原始程式碼管理是一種很好的部署選項，它支援更複雜的部署管道。

### <a name="local-git"></a>本機 Git

可以使用本地 Git 使用 Git 將代碼從本地電腦推送到 Azure 函數。

>__如何使用它：__ 按照本地 Git[部署到 Azure 應用服務](../app-service/deploy-local-git.md)中的說明進行操作。

>__何時使用它：__ 通常，我們建議您使用不同的部署方法。 從本地 Git 發佈時，必須[手動同步觸發器](#trigger-syncing)。

### <a name="cloud-sync"></a>雲同步

使用雲同步將內容從 Dropbox 和 OneDrive 同步到 Azure 功能。

>__如何使用它：__ 按照[雲資料夾中的"同步內容"中的](../app-service/deploy-content-sync.md)說明進行操作。

>__何時使用它：__ 通常，我們建議其他部署方法。 使用雲同步發佈時，必須[手動同步觸發器](#trigger-syncing)。

### <a name="ftp"></a>FTP

可以使用 FTP 將檔直接傳輸到 Azure 函數。

>__如何使用它：__[使用 FTP/s，按照部署內容](../app-service/deploy-ftp.md)中的說明操作。

>__何時使用它：__ 通常，我們建議其他部署方法。 使用 FTP 發佈時，必須[手動同步觸發器](#trigger-syncing)。

### <a name="portal-editing"></a>門戶編輯

在基於門戶的編輯器中，可以直接編輯函數應用中的檔（基本上每次保存更改時進行部署）。

>__如何使用它：__ 為了能夠在 Azure 門戶中編輯函數，必須在[門戶中創建了函數](functions-create-first-azure-function.md)。 要保留單個真實來源，使用任何其他部署方法可使函數為唯讀，並防止繼續進行門戶編輯。 要返回到可以在 Azure 門戶中編輯檔的狀態，可以手動將編輯模式重新轉向`Read/Write`並刪除任何與部署相關的應用程式設定（如`WEBSITE_RUN_FROM_PACKAGE`）。 

>__何時使用它：__ 門戶是開始使用 Azure 函數的好方法。 對於更密集的開發工作，我們建議您使用以下用戶端工具之一：
>
>* [視覺工作室代碼](functions-create-first-function-vs-code.md)
>* [Azure 函數核心工具（命令列）](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

下表顯示了支援門戶編輯的作業系統和語言：

| | 視窗消耗 | 視窗高級版 | 專用視窗 | Linux 消費 | Linux 高級版 | Linux 專用 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 指令碼 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (預覽) | | | | | | |
| PowerShell (預覽) |✔|✔|✔| | | |
| 類型腳本（Node.js） | | | | | | |

<sup>*</sup>門戶編輯僅針對 Linux 上使用高級和專用計畫的函數的 HTTP 和計時器觸發器啟用。

## <a name="deployment-slots"></a>部署位置

將函數應用部署到 Azure 時，可以部署到單獨的部署槽，而不是直接部署到生產。 有關部署槽的詳細資訊，請參閱[Azure 函數部署槽](../app-service/deploy-staging-slots.md)文檔以瞭解詳細資訊。

## <a name="next-steps"></a>後續步驟

閱讀以下文章以瞭解有關部署函數應用的更多資訊： 

+ [Azure Functions 的持續部署](functions-continuous-deployment.md)
+ [使用 Azure DevOps 連續傳遞](functions-how-to-azure-devops.md)
+ [Azure 函數的 Zip 部署](deployment-zip-push.md)
+ [從套件檔案執行 Azure Functions](run-functions-from-deployment-package.md)
+ [在 Azure 函數中自動為函數應用進行資源部署](functions-infrastructure-as-code.md)
