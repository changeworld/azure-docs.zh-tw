---
title: Azure Functions 中的部署技術
description: 瞭解可將程式碼部署到 Azure Functions 的不同方式。
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943633"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions 中的部署技術

您可以使用幾種不同的技術，將您的 Azure Functions 專案程式碼部署到 Azure。 本文概述可供您使用的部署方法，以及在各種案例中使用最佳方法的建議。 它也提供有關基礎部署技術的詳盡清單和重要詳細資料。 

## <a name="deployment-methods"></a>部署方法

您用來將程式碼發佈到 Azure 的部署技術，通常取決於您發佈應用程式的方式。 適當的部署方法取決於特定需求和開發週期中的點。 例如，在開發和測試期間，您可以直接從您的開發工具（例如 Visual Studio Code）進行部署。 當您的應用程式在生產環境中時，您更有可能會從原始檔控制或使用自動發行管線（包括額外的驗證和測試）持續發行。  

下表描述您函式專案的可用部署方法。

| 部署 &nbsp; 類型 | 方法 | 最適合 .。。 |
| -- | -- | -- |
| 以工具為基礎 | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; Code &nbsp; 發行](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio 發佈](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Core Tools 發佈](functions-run-local.md#publish) | 開發和其他 ad hock 部署期間的部署。 這些工具會在本機管理部署。 | 
| App Service 管理| &bull;&nbsp;[部署 &nbsp; 中心 &nbsp; (CI/CD) ](functions-continuous-deployment.md)<br/>&bull;&nbsp;[容器 &nbsp; 部署](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  持續部署 (從原始檔控制或從容器登錄) CI/CD。 部署由 App Service platform (Kudu) 來管理。|
| 外部管線|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub 動作](functions-how-to-github-actions.md) | 包含額外驗證、測試和其他動作的生產和 DevOps 管線會作為自動化部署的一部分來執行。 部署由管線管理。 |

雖然特定的函式部署會根據其內容使用最佳技術，但大部分的部署方法都是以 [zip 部署](#zip-deploy)為基礎。

## <a name="deployment-technology-availability"></a>部署技術可用性

Azure Functions 支援在 Windows 和 Linux 上進行跨平臺本機開發和裝載。 目前有三種主控方案可用：

+ [耗用量](consumption-plan.md)
+ [高級](functions-premium-plan.md)
+ [專用 (App Service) ](dedicated-plan.md)

每個方案都有不同的行為。 並非所有的部署技術都適用于 Azure Functions 的每個類別。 下圖顯示每個作業系統和主控方案組合支援的部署技術：

| 部署技術 | Windows 耗用量 | Windows Premium | Windows 專用  | Linux 使用量 | Linux Premium | Linux 專用 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 外部套件 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip 部署 |✔|✔|✔|✔|✔|✔|
| Docker 容器 | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| 原始檔控制 |✔|✔|✔| |✔|✔|
| 本機 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 雲端同步<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| 入口網站編輯 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> 個需要 [手動觸發同步](#trigger-syncing)處理的部署技術。
<sup>2</sup> 僅針對使用 Premium 和專用方案之 Linux 上的函式，啟用 HTTP 和計時器觸發程式的編輯。

## <a name="key-concepts"></a>重要概念

部分重要的概念對於瞭解部署在 Azure Functions 中的運作方式非常重要。

### <a name="trigger-syncing"></a>觸發同步處理

當您變更任何觸發程式時，函數基礎結構必須留意變更。 許多部署技術會自動進行同步處理。 不過，在某些情況下，您必須手動同步處理您的觸發程式。 當您透過參考外部套件 URL、本機 Git、雲端同步或 FTP 來部署更新時，您必須手動同步處理您的觸發程式。 您可以透過下列三種方式之一來同步處理觸發程式：

* 在 Azure 入口網站中重新開機函數應用程式
* 將 HTTP POST 要求傳送至 `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` 使用 [主要金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。
* 將 HTTP POST 要求傳送至 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` 。 將預留位置取代為您的訂用帳戶識別碼、資源組名，以及函式應用程式的名稱。

### <a name="remote-build"></a>遠端組建

Azure Functions 可以在 zip 部署之後，自動對它所收到的程式碼執行組建。 根據您的應用程式是否在 Windows 或 Linux 上執行，這些組建的行為會稍有不同。 當應用程式先前已設定為 [從封裝](run-functions-from-deployment-package.md) 模式執行時，不會執行遠端組建。 若要瞭解如何使用遠端組建，請流覽至「 [zip 部署](#zip-deploy)」。

> [!NOTE]
> 如果您在遠端組建方面遇到問題，可能是因為您的應用程式是在功能推出之前建立的， (2019 年8月1日) 。 嘗試建立新的函式應用程式，或執行 `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` 以更新您的函數應用程式。 此命令可能需要兩次嘗試才能成功。

#### <a name="remote-build-on-windows"></a>Windows 上的遠端組建

在 Windows 上執行的所有函式應用程式都有小型的管理應用程式，也就是 SCM (或 [Kudu](https://github.com/projectkudu/kudu)) 網站。 此網站會處理 Azure Functions 的許多部署和建立邏輯。

當應用程式部署至 Windows 時，會執行特定語言的命令，例如 `dotnet restore` (c # ) 或 `npm install` (JavaScript) 。

#### <a name="remote-build-on-linux"></a>Linux 上的遠端組建

若要在 Linux 上啟用遠端組建，必須設定下列 [應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings) ：

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

根據預設，在部署至 Linux 時，Visual Studio Code 會執行遠端組建的 [Azure Functions Core Tools](functions-run-local.md) 和 [Azure Functions 擴充](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) 功能。 因此，這兩個工具都會自動為您在 Azure 中建立這些設定。

當應用程式在 Linux 上從遠端建立時，它們會 [從部署套件執行](run-functions-from-deployment-package.md)。

##### <a name="consumption-plan"></a>使用情況方案

在取用方案中執行的 Linux 函式應用程式沒有 SCM/Kudu 網站，這會限制部署選項。 不過，在取用方案中執行的 Linux 上的函式應用程式確實支援遠端組建。

##### <a name="dedicated-and-premium-plans"></a>專用和 Premium 方案

在 Linux 上執行的函式應用程式在 [專用 (App Service) 方案](dedicated-plan.md) 和高階 [方案](functions-premium-plan.md) 中，也有限制的 SCM/Kudu 網站。

## <a name="deployment-technology-details"></a>部署技術詳細資料

下列是 Azure Functions 中提供的部署方法。

### <a name="external-package-url"></a>外部套件 URL

您可以使用外部套件 URL 來參考包含函式應用程式 ( .zip) 檔案的遠端封裝。 檔案是從提供的 URL 下載，而應用程式則是 [從封裝](run-functions-from-deployment-package.md) 模式執行。

>__使用方式：__ 新增 [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) 至您的應用程式設定。 這項設定的值應該是 URL (您要執行) 的特定套件檔案的位置。 您可以 [在入口網站中](functions-how-to-use-azure-function-app-settings.md#settings) 或 [使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)來新增設定。
>
>如果您使用 Azure Blob 儲存體，請使用具有共用存取簽章的私人容器 [ (SAS) ](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 來提供封裝的存取權。 應用程式每次重新開機時，都會提取內容的複本。 在應用程式的存留期內，您的參考必須有效。

>__使用時機：__ 如果使用者不想要進行 [遠端組建](#remote-build) ，則在取用方案中，針對在 Linux 上執行的 Azure Functions，唯一支援的部署方法是 [外部套件 URL]。 當您更新函式應用程式所參考的封裝檔案時，您必須 [手動同步處理觸發](#trigger-syncing) 程式，以告訴 Azure 您的應用程式已變更。

### <a name="zip-deploy"></a>Zip 部署

使用 zip deploy 將包含您函式應用程式的 .zip 檔案推送至 Azure。 （選擇性）您可以將應用程式設定為 [從套件](run-functions-from-deployment-package.md)開始執行，或指定 [遠端組建](#remote-build) 發生。

>__使用方式：__ 使用您最愛的用戶端工具部署： [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure)、 [Visual Studio](functions-develop-vs.md#publish-to-azure)，或從命令列使用 [Azure Functions Core Tools](functions-run-local.md#project-file-deployment)。 根據預設，這些工具會使用 zip 部署並 [從封裝執行](run-functions-from-deployment-package.md)。 Core Tools 和 Visual Studio Code 擴充功能都會在部署至 Linux 時啟用 [遠端組建](#remote-build) 。 若要手動將 .zip 檔案部署至您的函式應用程式，請遵循 [從 .zip 檔案或 URL 部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)的指示。

>當您使用 zip deploy 進行部署時，您可以將應用程式設定為 [從封裝執行](run-functions-from-deployment-package.md)。 若要從封裝執行，請將 [ `WEBSITE_RUN_FROM_PACKAGE` ] (函式-應用程式設定. md # website_run_from_package 應用程式設定值設定為 `1` 。 建議您進行 zip 部署。 它可為您的應用程式產生更快的載入時間，而且這是 VS Code、Visual Studio 和 Azure CLI 的預設值。

>__使用時機：__ Zip deploy 是建議的 Azure Functions 部署技術。

### <a name="docker-container"></a>Docker 容器

您可以部署包含函數應用程式的 Linux 容器映射。

>__使用方式：__ 在 Premium 或專用方案中建立 Linux 函數應用程式，並指定要執行的容器映射。 您可以使用兩種方式執行此動作：
>
>* 在 Azure 入口網站的 Azure App Service 方案上建立 Linux 函數應用程式。 針對 [ **發佈**]，請選取 [ **Docker 映射**]，然後設定容器。 輸入裝載映射的位置。
>* 使用 Azure CLI 在 App Service 方案上建立 Linux 函數應用程式。 若要深入瞭解，請參閱 [使用自訂映射在 Linux 上建立](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)函式。
>
>若要使用自訂容器部署至現有的應用程式，請在 [Azure Functions Core Tools](functions-run-local.md)中使用 [`func deploy`](functions-run-local.md#publish) 命令。

>__使用時機：__ 當您需要對執行函數應用程式的 Linux 環境進行更多的控制時，請使用 Docker 容器選項。 此部署機制僅適用于在 Linux 上執行的函式。

### <a name="web-deploy-msdeploy"></a>Web Deploy (Msdeploy.exe) 

Web Deploy 套件，並將您的 Windows 應用程式部署到任何 IIS 伺服器，包括在 Azure 的 Windows 上執行的函式應用程式。

>__使用方式：__ 使用 [Visual Studio 工具進行 Azure Functions](functions-create-your-first-function-visual-studio.md)。 清除 [ **從封裝檔案執行 (建議的)** ] 核取方塊。
>
>您也可以下載 [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) 並 `MSDeploy.exe` 直接呼叫。

>__使用時機：__ 支援 Web Deploy 且沒有任何問題，但是慣用的機制是「 [從啟用套件執行](#zip-deploy)」的「zip 部署」。 若要深入瞭解，請參閱 [Visual Studio 開發指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>原始檔控制

使用原始檔控制，將函數應用程式連接至 Git 存放庫。 該存放庫中的程式碼更新會觸發部署。 如需詳細資訊，請參閱 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__使用方式：__ 在入口網站的 [功能] 區域中使用 [部署中心]，設定從原始檔控制發行。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。

>__使用時機：__ 使用原始檔控制是小組在其函式應用程式上共同作業的最佳做法。 原始檔控制是絕佳的部署選項，可啟用更精密的部署管線。

### <a name="local-git"></a>本機 Git

您可以使用本機 Git，透過 Git 將程式碼從本機電腦推送至 Azure Functions。

>__使用方式：__ 遵循 [本機 Git 部署](../app-service/deploy-local-git.md)中的指示 Azure App Service。

>__使用時機：__ 一般來說，我們建議您使用不同的部署方法。 當您從本機 Git 發行時，您必須 [手動同步處理觸發](#trigger-syncing)程式。

### <a name="cloud-sync"></a>雲端同步

使用 cloud sync 將您的內容從 Dropbox 和 OneDrive 同步至 Azure Functions。

>__使用方式：__ 遵循 [從雲端資料夾同步內容](../app-service/deploy-content-sync.md)的指示。

>__使用時機：__ 一般來說，我們建議其他部署方法。 當您使用雲端同步發行時，您必須 [手動同步處理觸發](#trigger-syncing)程式。

### <a name="ftp"></a>FTP

您可以使用 FTP 直接將檔案傳送至 Azure Functions。

>__使用方式：__ 遵循 [使用 FTP/s 部署內容](../app-service/deploy-ftp.md)中的指示。

>__使用時機：__ 一般來說，我們建議其他部署方法。 當您使用 FTP 進行發佈時，您必須 [手動同步處理觸發](#trigger-syncing)程式。

### <a name="portal-editing"></a>入口網站編輯

在以入口網站為基礎的編輯器中，您可以直接編輯函式應用程式中的檔案， (基本上是在每次儲存變更) 時部署。

>__使用方式：__ 若要能夠在 Azure 入口網站中編輯您的函式，您必須 [在入口網站中建立您的函數](./functions-get-started.md)。 若要保留單一真實來源，使用任何其他部署方法可讓您的函式變成隻讀，並防止繼續進行入口網站編輯。 若要返回您可以在 Azure 入口網站中編輯檔案的狀態，您可以手動將編輯模式切換回， `Read/Write` 並移除任何部署相關的應用程式設定， (像這樣 [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) 。

>__使用時機：__ 入口網站是開始使用 Azure Functions 的好方法。 針對更密集的開發工作，建議您使用下列其中一種用戶端工具：
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (命令列) ](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

下表顯示支援入口網站編輯的作業系統和語言：

| Language | Windows 耗用量 | Windows Premium | Windows 專用 | Linux 使用量 | Linux Premium | Linux 專用 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 指令碼 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (預覽) | | | | | | |
| PowerShell (預覽) |✔|✔|✔| | | |
| TypeScript ( # A0)  | | | | | | |

<sup>*</sup> 只有在 Linux 上使用 Premium 和專用方案的 HTTP 和計時器觸發程式才會啟用入口網站編輯。

## <a name="deployment-behaviors"></a>部署行為

當您進行部署時，所有現有的執行都可以完成或超時，之後便會載入新的程式碼以開始處理要求。

如果您需要更充分掌控此轉換，您應該使用部署位置。

## <a name="deployment-slots"></a>部署位置

當您將函數應用程式部署至 Azure 時，您可以部署到個別的部署位置，而不是直接部署到生產環境。 如需部署位置的詳細資訊，請參閱 [Azure Functions 部署](functions-deployment-slots.md) 位置檔以取得詳細資料。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何部署函數應用程式，請參閱下列文章：

+ [Azure Functions 的持續部署](functions-continuous-deployment.md)
+ [使用 Azure DevOps 持續傳遞](functions-how-to-azure-devops.md)
+ [Azure Functions 的 Zip 部署](deployment-zip-push.md)
+ [從套件檔案執行 Azure Functions](run-functions-from-deployment-package.md)
+ [在 Azure Functions 中將函數應用程式的資源部署自動化](functions-infrastructure-as-code.md)
