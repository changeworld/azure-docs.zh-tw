---
title: 使用 Azure 函數和其他服務構建無伺服器移動應用程式後端
description: 瞭解用於構建可靠、無伺服器的後端移動應用程式的計算服務。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240136"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>使用計算服務構建移動後端元件
每個移動應用程式都需要一個後端，負責資料存儲、業務邏輯和安全。 管理基礎結構以託管和執行後端代碼需要您調整大小、預配和擴展多個伺服器。 您還必須管理作業系統更新和所涉及的硬體，並應用安全修補程式。 然後，您需要監視所有這些基礎結構元件的性能、可用性和容錯性。 

無伺服器體系結構對於此類方案非常有用，因為您沒有伺服器可供管理，也沒有作業系統或相關的軟體或硬體更新可供管理。 無伺服器體系結構可節省開發人員的時間和成本，這意味著更快的上市時間和將精力集中在建築應用程式上。

## <a name="benefits-of-compute"></a>計算的好處
- 伺服器抽象意味著無需擔心託管、修補和安全，這允許您只關注代碼。
- 即時高效的擴展可確保以所需的任何規模自動或按需調配資源。
- 高可用性和容錯能力。
- 微計費可確保僅在代碼實際運行時為您付費。
- 代碼在以您選擇的語言編寫的雲中運行。

使用以下服務在移動應用中啟用無伺服器計算功能。

## <a name="azure-functions"></a>Azure Functions
[Azure 函數](https://azure.microsoft.com/services/functions/)是一種事件驅動的計算體驗，可用於執行代碼，這些代碼使用您選擇的程式設計語言編寫，而無需擔心伺服器。 您不必管理應用程式或基礎結構來運行它。 函數可按需擴展，並且僅為代碼運行時付費。 Azure 函數是實現移動應用程式 API 的好方法。 它們易於實現和維護，可通過 HTTP 訪問。

**主要功能**
- 事件驅動和可擴展，您可以使用觸發器和綁定來定義何時調用函數及其連接的資料。
- 自帶依賴項，因為函數支援 NuGet 和 NPM，因此您可以使用您喜愛的庫。
- 集成安全性，以便您可以使用 OAuth 供應商（如 Azure 活動目錄、Facebook、Google、Twitter 和 Microsoft 帳戶）保護 HTTP 觸發的功能。
- 簡化與不同[Azure 服務和](/azure/azure-functions/functions-overview)軟體即服務 （SaaS） 產品集成。
- 靈活的開發，以便您可以直接在 Azure 門戶中編寫函數代碼，或者設置持續集成，並通過 GitHub、Azure DevOps 服務和其他受支援的開發工具部署代碼。
- 函數運行時是開源的，在[GitHub](https://github.com/azure/azure-webjobs-sdk-script)上可用。
- 通過集成工具和內置 DevOps 功能進行監視，增強開發體驗，您可以在本地使用首選編輯器或便於使用的 Web 介面進行編碼、測試和調試。
- 各種程式設計語言和用於開發宿主選項，如 C#、Node.js、JAVA、JavaScript 或 Python。
- 按使用量付費定價模型意味著您只為運行代碼所花費的時間付費。

**引用**
- [Azure 門戶](https://portal.azure.com)
- [Azure 函數文檔](/azure/azure-functions/)
- [Azure Functions 開發人員指南](/azure/azure-functions/functions-reference)
- [快速入門](/azure/azure-functions/functions-create-first-function-vs-code)
- [樣品](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
使用[Azure 應用服務](https://azure.microsoft.com/services/app-service/)，您可以以您選擇的程式設計語言構建和託管 Web 應用和 RESTful API，而無需管理基礎結構。 它提供自動縮放和高可用性，同時支援 Windows 和 Linux，並支援從 GitHub、Azure DevOps 或任何 Git 存儲庫進行自動部署。

**主要功能**
- 對ASP.NET、ASP.NET核心、JAVA、Ruby、Node.js、PHP 或 Python 的多種語言和框架支援。 您也可以將 PowerShell 和其他指令碼或可執行檔 做為背景服務來執行。
- 通過與 Azure DevOps、GitHub、BitBucket、Docker 中心或 Azure 容器註冊表的持續集成和部署進行 DevOps 優化。 使用 Azure PowerShell 或跨平台命令列介面 (CLI)，在 App Service 中管理您的應用程式。
- 具有高可用性的全域規模，可手動或自動擴展或擴展。
- 連接到 SaaS 平臺和本地資料，從 50 多個連接器中選擇用於企業系統（如 SAP、SaaS 服務（如 Salesforce）和互聯網服務（如 Facebook）。 使用混合連接和 Azure 虛擬網路訪問本地資料。
- Azure 應用服務符合 ISO、SOC 和 PCI 標準。 使用 Azure 活動目錄或登錄 Google、Facebook、Twitter 和微軟等社交媒體的使用者進行身份驗證。 建立 IP 位址限制和管理服務身分識別。
- 應用程式範本可從 Azure 應用商店中的應用程式範本（如 WordPress、Joomla 和 Drupal）中大量選擇。
- Visual Studio 與 Visual Studio 中的專用工具集成可簡化創建、部署和調試的工作。

**引用**
- [Azure 門戶](https://portal.azure.com/)
- [Azure 應用服務文件](/azure/app-service/)
- [快速入門](/azure/app-service/app-service-web-get-started-dotnet)
- [樣品](/azure/app-service/samples-cli)
- [教學課程](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 會管理裝載的 Kubernetes 環境。 AKS 可快速且輕鬆地部署和管理容器化的應用程式，而不需要容器協調流程專業知識。 它還消除了持續運營和維護的負擔。 AKS 可按需提供、升級和擴展資源，而不會使應用程式離線。

**主要功能**
- 輕鬆將現有應用程式遷移到容器並在 AKS 中運行。
- 簡化基於微服務的應用程式的部署和管理。
- 為 AKS 提供安全 DevOps，實現速度和安全性之間的平衡，並更快地大規模交付代碼。
- 通過使用 AKS 和 Azure 容器實例在容器實例中預配以秒為單位的窗格，輕鬆擴展。
- 按需部署和管理 IoT 設備。
- 使用 TensorFlow 和 KubeFlow 等工具對機器學習模型進行培訓。

**引用**
- [Azure 門戶](https://portal.azure.com/)
- [Azure 庫伯內斯服務文件](/azure/aks/)
- [快速入門](/azure/aks/kubernetes-walkthrough-portal)
- [教學課程](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure 容器實例](https://azure.microsoft.com/services/container-instances/)是可在隔離容器中操作的任何方案（如簡單應用程式、任務自動化和生成作業）的絕佳解決方案。 無需管理 VM 即可快速開發應用。

**主要功能**
- 容器實例的快速啟動時間可以在數秒內在 Azure 中啟動容器，而無需預配和管理 VM。
- 公共 IP 連接和自訂 DNS 名稱。
- 虛擬機器管理程式級安全性，可確保應用程式與在 VM 中隔離一樣。
- 通過允許 CPU 內核和記憶體的精確規格，自訂大小以獲得最佳利用率。 您可根據所需的數量付費並採用以秒計費方式，因此能夠根據實際的需求來微調您的費用。
- 用於檢索和保留狀態的持久存儲。 容器實例提供直接安裝 Azure 檔共用。
- 使用同一 API 安排的 Linux 和 Windows 容器。

**引用**
- [Azure 門戶](https://portal.azure.com/)
- [Azure 容器實例文檔](/azure/container-instances/)
- [快速入門](/azure/container-instances/container-instances-quickstart-portal)
- [樣品](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [教學課程](/azure/container-instances/container-instances-tutorial-prepare-app)