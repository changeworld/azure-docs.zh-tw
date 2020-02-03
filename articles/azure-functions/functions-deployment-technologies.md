---
title: Azure Functions 中的部署技術
description: 瞭解您可以將程式碼部署到 Azure Functions 的不同方式。
author: ColbyTresness
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 39b3a96ddd3a9007600d1fa956c4e2d48f6adae9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714799"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions 中的部署技術

您可以使用幾種不同的技術，將您的 Azure Functions 專案程式碼部署至 Azure。 本文提供這些技術的詳盡清單，說明哪些技術適用于哪些類別的函式、說明當您使用每個方法時所發生的情況，以及提供在各種情況下使用最佳方法的建議. 支援部署至 Azure Functions 的各種工具，會根據其內容調整為正確的技術。 一般來說，zip 部署是建議的 Azure Functions 部署技術。

## <a name="deployment-technology-availability"></a>部署技術可用性

Azure Functions 支援跨平臺本機開發和 Windows 和 Linux 上的裝載。 目前有三個主控方案可供使用：

+ [率](functions-scale.md#consumption-plan)
+ [高級](functions-scale.md#premium-plan)
+ [專用（App Service）](functions-scale.md#app-service-plan)

每個方案都有不同的行為。 並非所有的部署技術都適用于 Azure Functions 的各個類別。 下圖顯示作業系統和主控方案的每個組合支援的部署技術：

| 部署技術 | Windows 耗用量 | Windows Premium | Windows 專用  | Linux 使用量 | Linux Premium | Linux 專用 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 外部套件 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip 部署 |✔|✔|✔|✔|✔|✔|
| Docker 容器 | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| 原始檔控制 |✔|✔|✔| |✔|✔|
| 本機 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 雲端同步處理<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| 入口網站編輯 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup>需要[手動觸發同步](#trigger-syncing)處理的部署技術。  
<sup>2</sup>只有針對 Linux 上的函式使用 Premium 和專用方案的 HTTP 和計時器觸發程式，才會啟用入口網站編輯功能。

## <a name="key-concepts"></a>重要概念

若要瞭解部署在 Azure Functions 中的使用方式，某些重要概念十分重要。

### <a name="trigger-syncing"></a>觸發程式同步

當您變更任何觸發程式時，函數基礎結構必須知道這些變更。 同步處理會針對許多部署技術自動進行。 不過，在某些情況下，您必須手動同步處理您的觸發程式。 當您藉由參考外部套件 URL、本機 Git、雲端同步或 FTP 來部署更新時，您必須手動同步處理您的觸發程式。 您可以透過下列三種方式之一來同步觸發程式：

* 在 Azure 入口網站中重新開機函數應用程式
* 使用[主要金鑰](functions-bindings-http-webhook.md#authorization-keys)將 HTTP POST 要求傳送至 `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`。
* 將 HTTP POST 要求傳送至 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`。 將預留位置取代為您的訂用帳戶識別碼、資源組名和函式應用程式的名稱。

### <a name="remote-build"></a>遠端組建

Azure Functions 可以自動在 zip 部署後所收到的程式碼上執行組建。 根據您的應用程式是在 Windows 或 Linux 上執行，這些組建的行為會稍有不同。 當先前已將應用程式設定為在 [[從封裝執行](run-functions-from-deployment-package.md)] 中執行時，不會執行遠端組建。 若要瞭解如何使用遠端組建，請流覽至 [ [zip 部署](#zip-deploy)]。

> [!NOTE]
> 如果您遇到遠端組建的問題，可能是因為您的應用程式是在功能推出之前建立（2019年8月1日）。 請嘗試建立新的函式應用程式，或執行 `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` 來更新您的函式應用程式。 此命令可能會嘗試兩次成功。

#### <a name="remote-build-on-windows"></a>Windows 上的遠端組建

在 Windows 上執行的所有函數應用程式都有小型的管理應用程式，也就是 SCM （或[Kudu](https://github.com/projectkudu/kudu)）網站。 此網站會處理許多部署和 Azure Functions 的組建邏輯。

將應用程式部署到 Windows 時，會執行特定語言的命令，例如C#`dotnet restore` （）或 `npm install` （JavaScript）。

#### <a name="remote-build-on-linux"></a>Linux 上的遠端組建

若要在 Linux 上啟用遠端組建，必須設定下列[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)：

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

根據預設，在部署至 Linux 時， [Azure Functions Core Tools](functions-run-local.md)和[Azure Functions Visual Studio Code 延伸](functions-create-first-function-vs-code.md#publish-the-project-to-azure)模組都會執行遠端組建。 因此，這兩個工具會自動在 Azure 中為您建立這些設定。 

當應用程式在 Linux 上以遠端方式建立時，它們會[從部署套件執行](run-functions-from-deployment-package.md)。 

##### <a name="consumption-plan"></a>取用方案

在取用方案中執行的 Linux 函式應用程式沒有 SCM/Kudu 網站，這會限制部署選項。 不過，在取用方案中執行的 Linux 上的函式應用程式會支援遠端組建。

##### <a name="dedicated-and-premium-plans"></a>專用和 Premium 方案

在[專用（App Service）方案](functions-scale.md#app-service-plan)和[Premium 方案](functions-scale.md#premium-plan)的 Linux 上執行的函式應用程式，也有受限的 SCM/Kudu 網站。

## <a name="deployment-technology-details"></a>部署技術詳細資料

Azure Functions 提供下列部署方法。

### <a name="external-package-url"></a>外部封裝 URL

您可以使用外部套件 URL 來參考包含函數應用程式的遠端封裝（.zip）檔案。 檔案會從提供的 URL 下載，而應用程式會在 [[從封裝執行](run-functions-from-deployment-package.md)] 模式下執行。

>__使用方式：__ 將 `WEBSITE_RUN_FROM_PACKAGE` 新增至您的應用程式設定。 此設定的值應該是 URL （您要執行之特定封裝檔案的位置）。 您可以[在入口網站中](functions-how-to-use-azure-function-app-settings.md#settings)或[使用 [Azure CLI] 來](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)新增設定。 
>
>如果您使用 Azure Blob 儲存體，請使用具有[共用存取簽章（SAS）](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)的私人容器，為函式提供對封裝的存取權。 每當應用程式重新開機時，它就會提取內容的複本。 您的參考在應用程式的存留期內必須是有效的。

>__使用時機：__ 如果使用者不想要進行[遠端組建](#remote-build)，則 [外部套件 URL] 是在取用方案的 Linux 上執行 Azure Functions 唯一支援的部署方法。 當您更新函式應用程式所參考的封裝檔案時，您必須[手動同步處理觸發](#trigger-syncing)程式，以告知 Azure 您的應用程式已變更。

### <a name="zip-deploy"></a>Zip 部署

使用「zip 部署」將包含函數應用程式的 .zip 檔案推送至 Azure。 （選擇性）您可以將應用程式設定為[從封裝](run-functions-from-deployment-package.md)開始執行，或指定發生[遠端組建](#remote-build)。

>__使用方式：__ 使用您最愛的用戶端工具進行部署： [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)、 [Visual Studio](functions-develop-vs.md#publish-to-azure)、 [Azure Functions Core Tools](functions-run-local.md)或[Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)。 根據預設，這些工具會使用 zip 部署，並[從封裝執行](run-functions-from-deployment-package.md)。 當部署至 Linux 時，核心工具和 Visual Studio Code 延伸模組都會啟用[遠端組建](#remote-build)。 若要將 .zip 檔案手動部署至函式應用程式，請遵循[從 .zip 檔案或 URL 部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)中的指示。

>當您使用「zip 部署」進行部署時，您可以將應用程式設定為[從封裝執行](run-functions-from-deployment-package.md)。 若要從封裝執行，請將 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定值設定為 `1`。 我們建議您進行 zip 部署。 它會為您的應用程式產生更快的載入時間，而且它是 VS Code、Visual Studio 和 Azure CLI 的預設值。 

>__使用時機：__ Zip 部署是建議的 Azure Functions 部署技術。

### <a name="docker-container"></a>Docker 容器

您可以部署包含函數應用程式的 Linux 容器映射。

>__使用方式：__ 在 Premium 或專屬方案中建立 Linux 函數應用程式，並指定要執行的容器映射。 您可以使用兩種方式執行此動作：
>
>* 在 Azure 入口網站中的 Azure App Service 方案上建立 Linux 函數應用程式。 針對 [**發佈**]，選取 [ **Docker 映射**]，然後設定容器。 輸入裝載映射的位置。
>* 使用 Azure CLI，在 App Service 方案上建立 Linux 函數應用程式。 若要瞭解作法，請參閱[使用自訂映射在 Linux 上建立](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)函式。
>
>若要使用自訂容器部署至現有的應用程式，請在[Azure Functions Core Tools](functions-run-local.md)中，使用[`func deploy`](functions-run-local.md#publish)命令。

>__使用時機：__ 當您需要更充分掌控您的函式應用程式執行所在的 Linux 環境時，請使用 [Docker 容器] 選項。 此部署機制僅適用于在 Linux 上執行的函式。

### <a name="web-deploy-msdeploy"></a>Web Deploy （Msdeploy.exe）

Web Deploy 封裝，並將您的 Windows 應用程式部署至任何 IIS 伺服器，包括在 Azure 中的 Windows 上執行的函數應用程式。

>__使用方式：__ 使用[適用于 Azure Functions 的 Visual Studio 工具](functions-create-your-first-function-visual-studio.md)。 清除 [**從套件檔案執行（建議選項）** ] 核取方塊。
>
>您也可以下載[Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy)並直接呼叫 `MSDeploy.exe`。

>__使用時機：__ 支援 Web Deploy 且沒有任何問題，但慣用的機制是「 [zip 部署已啟用從封裝執行](#zip-deploy)」。 若要深入瞭解，請參閱[Visual Studio 開發指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>原始檔控制

使用原始檔控制，將您的函式應用程式連線至 Git 存放庫。 該存放庫中的程式碼更新會觸發部署。 如需詳細資訊，請參閱[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__使用方式：__ 在入口網站的 [功能] 區域中使用 [部署中心]，設定從原始檔控制發行。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。

>__使用時機：__ 使用原始檔控制是在其函式應用程式上共同作業之小組的最佳作法。 原始檔控制是不錯的部署選項，可啟用更複雜的部署管線。

### <a name="local-git"></a>本機 Git

您可以使用本機 Git，將程式碼從本機電腦推送至使用 Git Azure Functions。

>__使用方式：__ 依照[本機 Git 部署](../app-service/deploy-local-git.md)中的指示，Azure App Service。

>__使用時機：__ 一般來說，我們建議您使用不同的部署方法。 當您從本機 Git 發行時，您必須[手動同步處理觸發](#trigger-syncing)程式。

### <a name="cloud-sync"></a>雲端同步處理

使用雲端同步處理，將您的內容從 Dropbox 和 OneDrive 同步到 Azure Functions。

>__使用方式：__ 請遵循[從雲端資料夾同步內容](../app-service/deploy-content-sync.md)中的指示。

>__使用時機：__ 一般來說，我們建議其他部署方法。 當您使用雲端同步處理發佈時，您必須[手動同步處理觸發](#trigger-syncing)程式。

### <a name="ftp"></a>FTP

您可以使用 FTP 直接將檔案傳送到 Azure Functions。

>__使用方式：__ 遵循[使用 FTP/s 部署內容](../app-service/deploy-ftp.md)中的指示。

>__使用時機：__ 一般來說，我們建議其他部署方法。 當您使用 FTP 發行時，必須[手動同步處理觸發](#trigger-syncing)程式。

### <a name="portal-editing"></a>入口網站編輯

在以入口網站為基礎的編輯器中，您可以直接編輯函式應用程式中的檔案（基本上是在每次儲存變更時部署）。

>__使用方式：__ 若要能夠在 Azure 入口網站中編輯您的函式，您必須[在入口網站中建立您的函數](functions-create-first-azure-function.md)。 若要保留單一事實來源，使用任何其他部署方法會使您的函式成為唯讀，並防止繼續進行入口網站編輯。 若要返回您可以在 Azure 入口網站中編輯檔案的狀態，您可以手動將編輯模式切換回 `Read/Write`，並移除任何與部署相關的應用程式設定（例如 `WEBSITE_RUN_FROM_PACKAGE`）。 

>__使用時機：__ 入口網站是開始使用 Azure Functions 的好方法。 如需更密集的開發工作，建議您使用下列其中一個用戶端工具：
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools （命令列）](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

下表顯示支援入口網站編輯的作業系統和語言：

| | Windows 耗用量 | Windows Premium | Windows 專用 | Linux 使用量 | Linux Premium | Linux 專用 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 指令碼 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (預覽) | | | | | | |
| PowerShell （預覽） |✔|✔|✔| | | |
| TypeScript （node.js） | | | | | | |

<sup>*</sup>只有針對 Linux 上的函式使用 Premium 和專用方案的 HTTP 和計時器觸發程式，才會啟用入口網站編輯功能。

## <a name="deployment-slots"></a>部署位置

當您將函數應用程式部署至 Azure 時，您可以部署到個別的部署位置，而不是直接部署到生產環境。 如需部署位置的詳細資訊，請參閱[Azure Functions 部署](../app-service/deploy-staging-slots.md)位置檔，以取得詳細資料。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何部署函數應用程式，請閱讀下列文章： 

+ [Azure Functions 的持續部署](functions-continuous-deployment.md)
+ [使用 Azure DevOps 的持續傳遞](functions-how-to-azure-devops.md)
+ [Azure Functions 的 Zip 部署](deployment-zip-push.md)
+ [從封裝檔案執行您的 Azure Functions](run-functions-from-deployment-package.md)
+ [在 Azure Functions 中將函數應用程式的資源部署自動化](functions-infrastructure-as-code.md)
