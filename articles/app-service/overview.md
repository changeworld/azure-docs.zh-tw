---
title: 概觀
description: 了解 Azure App Service 如何協助您開發和裝載 Web 應用程式
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b72224441741990a1fc94400dfe718ea9d1b0b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961477"
---
# <a name="app-service-overview"></a>App Service 概觀

*Azure App Service* 是 HTTP 型服務，用來裝載 Web 應用程式、REST API 和行動後端。 您可以使用您慣用的語言進行開發，不管是 .NET、.NET Core、Java、Ruby、Node.js、PHP 還是 Python 都可以。 應用程式在 Windows 和 [Linux](#app-service-on-linux) 型環境中都可輕易執行及調整。

App Service 不只能將 Microsoft Azure 的功能新增到您的應用程式，例如安全性、負載平衡、自動調整和自動化管理。 您也可以利用其 DevOps 功能，例如從 Azure DevOps、GitHub、Docker Hub 和其他來源進行持續部署、套件管理、預備環境、自訂網域和 TLS/SSL 憑證。 

在使用 App Service 時，您只需就您所使用的 Azure 計算資源支付費用。 您所使用的計算資源取決於您用來執行應用程式的 _App Service 方案_。 如需詳細資訊，請參閱 [Azure App Service 方案概觀](overview-hosting-plans.md)。

## <a name="why-use-app-service"></a>為何要使用 App Service？

以下是 App Service 的一些主要功能︰

* **多種語言和架構** - App Service 具有 ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP 或 Python 的第一級支援。 您也可以將 [PowerShell 和其他指令碼或可執行檔](webjobs-create.md) 做為背景服務來執行。
* **受控的實際執行環境** - App Service 會自動為您[修補及維護 OS 和語言架構](overview-patch-os-runtime.md)。 您可以將時間用在撰寫絕佳的應用程式上，而讓 Azure 來擔心平台問題。
* **容器化和 Docker** - 將您的應用程式 Docker 化，並在 App Service 中裝載自訂的 Windows 或 Linux 容器。 使用 Docker Compose 執行多容器應用程式。 將您的 Docker 技能直接遷移到 App Service。
* **DevOps 最佳化** - 使用 Azure DevOps、GitHub、BitBucket、Docker Hub 或 Azure Container Registry 設定[持續整合和部署](deploy-continuous-deployment.md)。 透過 [測試和預備環境](deploy-staging-slots.md)升級更新。 使用 [Azure PowerShell](/powershell/azure/) 或[跨平台命令列介面 (CLI)](/cli/azure/install-azure-cli)，在 App Service 中管理您的應用程式。
* **具高可用性的全域調整** - 以手動或自動方式相應[增加](manage-scale-up.md)或[放大](../azure-monitor/platform/autoscale-get-started.md)。 在 Microsoft 的通用資料中心基礎結構中隨處裝載您的應用程式，而 App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 會承諾高可用性。
* **SaaS 平台和內部部署資料的連線** - 有超過 50 種適用於企業系統 (例如 SAP)、SaaS 服務 (例如 Salesforce) 和網際網路服務 (例如 Facebook) 的[連接器](../connectors/apis-list.md)可供選擇。 使用[混合式連線](app-service-hybrid-connections.md)和 [Azure 虛擬網路](web-sites-integrate-with-vnet.md)存取內部部署資料。
* **安全性和法規遵循** - App Service 為 [ISO、SOC 和 PCI 相容](https://www.microsoft.com/en-us/trustcenter)。 使用 [Azure Active Directory](configure-authentication-provider-aad.md)、[Google](configure-authentication-provider-google.md)、[Facebook](configure-authentication-provider-facebook.md)、[Twitter](configure-authentication-provider-twitter.md) 或 [Microsoft 帳戶](configure-authentication-provider-microsoft.md)來驗證使用者。 建立 [IP 位址限制](app-service-ip-restrictions.md)和[管理服務身分識別](overview-managed-identity.md)。
* **應用程式範本** - 從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 中的廣泛應用程式範本清單中進行選擇，例如 WordPress、Joomla 和 Drupal。
* **Visual Studio 和 Visual Studio Code 整合** - Visual Studio 和 Visual Studio Code 中的專用工具可簡化建立、部署和偵錯的工作。
* **API 和行動功能** - App Service 可提供適用於 RESTful API 案例的現成 CORS 支援，並可藉由啟用驗證、離線資料同步和推播通知等功能，簡化行動應用程式案例。
* **無伺服器程式碼** - 可隨需執行程式碼片段或指令碼，而不必明確佈建或管理基礎結構，而且只須就程式碼實際使用的計算時間支付費用 (請參閱 [Azure Functions](../azure-functions/index.yml))。

除了 App Service，Azure 還提供可用來裝載網站和 Web 應用程式的其他服務。 在大部分的情況下，App Service 是最佳選擇。  若是微服務架構，請考慮使用 [Azure Spring 雲端服務](../spring-cloud/index.yml)或 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。  如果您需要能更加充分地掌控執行程式碼的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。 如需如何在這些 Azure 服務之間做選擇的詳細資訊，請參閱 [Azure App Service、虛擬機器、Service Fabric 及雲端服務的比較](/azure/architecture/guide/technology-choices/compute-decision-tree)。

## <a name="app-service-on-linux"></a>Linux 上的 App Service

App Service 也可以針對支援的應用程式堆疊，在 Linux 上以原生方式裝載 Web 應用程式。 其也可以執行自訂的 Linux 容器 (也稱為用於容器的 Web App)。

### <a name="built-in-languages-and-frameworks"></a>內建語言和架構

Linux 上的 App Service 支援許多語言特定的內建映像。 請直接部署您的程式碼即可。 支援的語言包括：Node.js、Java (JRE 8 和 JRE 11)、PHP、Python、.NET Core 和 Ruby。 執行 [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) 以檢視最新的語言和支援的版本。 如果內建映像不支援應用程式所需的執行階段，您可以使用自訂容器進行部署。

### <a name="limitations"></a>限制

- [共用](https://azure.microsoft.com/pricing/details/app-service/plans/)定價層不支援 Linux 上的 App Service。 
- 您無法在相同的 App Service 方案中混合使用 Windows 和 Linux 應用程式。  
- 在相同的資源群組內，您無法在相同的區域中混合使用 Windows 和 Linux 應用程式。
- Azure 入口網站只會顯示目前適用於 Linux 應用程式的功能。 功能啟用之後，就會在入口網站上啟動。
- 在部署至內建映像時，您的程式碼和內容會配置給 Web 內容的存放磁碟區，並受到 Azure 儲存體支援。 相較於容器檔案系統的延遲，此磁碟區的磁碟延遲比較高且變化較大。 自訂容器選項可能有益於需要對於內容檔案進行大量唯讀存取的應用程式，這類應用程式會將檔案放在容器檔案系統上，而非內容磁碟區上。

## <a name="next-steps"></a>後續步驟

建立您的第一個 Web 應用程式。

> [!div class="nextstepaction"]
> [ASP.NET Core (在 Windows 或 Linux 上)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (在 Windows 上)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (在 Windows 或 Linux 上)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (在 Linux 上)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (在 Windows 或 Linux 上)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (在 Windows 或 Linux 上)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (位於 Linux 上)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (在 Windows 或 Linux 上)](quickstart-html.md)

> [!div class="nextstepaction"]
> [自訂容器 (Windows 或 Linux)](tutorial-custom-container.md)