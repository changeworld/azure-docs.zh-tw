---
title: 使用 Azure Functions 和其他服務建立無伺服器行動應用程式後端
description: 瞭解用來建立穩固、無伺服器行動應用程式後端的計算服務。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240136"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>使用計算服務建立行動後端元件
每個行動應用程式都需要一個後端，負責資料儲存、商務邏輯和安全性。 若要管理基礎結構來裝載和執行後端程式碼，您需要調整、布建及調整多部伺服器。 您也必須管理作業系統更新和相關硬體，並套用安全性修補程式。 接著，您必須監視所有這些基礎結構元件的效能、可用性和容錯能力。 

無伺服器架構適用于這種類型的案例，因為您沒有可管理的伺服器，也不需要管理作業系統或相關軟體或硬體更新。 無伺服器架構可節省開發人員的時間和成本，這表示在建立應用程式時，更快上市和專注的能源。

## <a name="benefits-of-compute"></a>計算的優點
- 伺服器的抽象意味著不需要擔心裝載、修補和安全性，這可讓您只專注于程式碼。
- 立即且有效率的調整可確保資源會根據您所需的任何規模，自動或依需求布建。
- 高可用性和容錯。
- 微計費可確保只有在您的程式碼實際執行時才會向您收取費用。
- 程式碼會在雲端中執行，並以您選擇的語言撰寫。

使用下列服務，在您的行動應用程式中啟用無伺服器計算功能。

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/)是事件驅動的計算體驗，可讓您用來執行程式碼（以您選擇的程式設計語言撰寫），而不需要擔心伺服器。 您不需要管理應用程式或基礎結構，就可以在上執行它。 函式會依需求進行調整，而您只需支付程式碼執行的時間。 Azure 函式是為行動應用程式執行 API 的絕佳方式。 它們很容易執行和維護，而且可以透過 HTTP 存取。

**主要功能**
- 事件驅動和可調整的位置，您可以在其中使用觸發程式和系結來定義叫用函式的時間，以及它所連接的資料。
- 帶入您自己的相依性，因為函式支援 NuGet 和 NPM，因此您可以使用您最愛的程式庫。
- 整合式安全性，讓您可以使用 OAuth 提供者（例如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶）保護 HTTP 觸發的函式。
- 簡化與不同[Azure 服務](/azure/azure-functions/functions-overview)和軟體即服務（SaaS）供應專案的整合。
- 彈性的開發，讓您可以直接在 Azure 入口網站中撰寫函式的程式碼，或透過 GitHub、Azure DevOps Services 和其他支援的開發工具來設定持續整合和部署程式碼。
- 函式執行時間是開放原始碼，並可在[GitHub](https://github.com/azure/azure-webjobs-sdk-script)上取得。
- 增強的開發體驗，讓您可以使用其慣用的編輯器或便於使用的 web 介面，透過整合式工具和內建的 DevOps 功能，在本機進行程式碼、測試和調試。
- 用於開發的各種程式設計語言和裝載選項，例如 c #、node.js、JAVA、JavaScript 或 Python。
- 按使用次數付費的定價模型表示您只需支付執行程式碼所花費的時間。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Azure Functions 文件](/azure/azure-functions/)
- [Azure Functions 開發人員指南](/azure/azure-functions/functions-reference)
- [快速入門](/azure/azure-functions/functions-create-first-function-vs-code)
- [範例](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
有了[Azure App Service](https://azure.microsoft.com/services/app-service/)，您就能以您選擇的程式設計語言來建立和裝載 web 應用程式和 RESTful api，而不需要管理基礎結構。 它提供自動調整和高可用性、同時支援 Windows 和 Linux，並可從 GitHub、Azure DevOps 或任何 Git 存放庫進行自動化部署。

**主要功能**
- 適用于 ASP.NET、ASP.NET Core、JAVA、Ruby、node.js、PHP 或 Python 的多種語言和架構支援。 您也可以將 PowerShell 和其他指令碼或可執行檔 做為背景服務來執行。
- 透過 Azure DevOps、GitHub、BitBucket、Docker Hub 或 Azure Container Registry 的持續整合和部署來 DevOps 優化。 使用 Azure PowerShell 或跨平台命令列介面 (CLI)，在 App Service 中管理您的應用程式。
- 具有高可用性的全球規模，可手動或自動相應增加或放大。
- 連線至 SaaS 平臺和內部部署資料，以針對企業系統（例如 SAP、SaaS 服務（例如 Salesforce）和網際網路服務（例如 Facebook）的50個連接器進行選擇。 使用混合式連接和 Azure 虛擬網路存取內部部署資料。
- Azure App Service 為 ISO、SOC 和 PCI 相容。 使用 Azure Active Directory 或使用社交媒體（例如 Google、Facebook、Twitter 和 Microsoft）的登入來驗證使用者。 建立 IP 位址限制和管理服務身分識別。
- 應用程式範本，可從 Azure Marketplace 中的廣泛應用程式範本清單中進行選擇，例如 WordPress、Joomla 和 Drupal。
- Visual Studio 整合 Visual Studio 中的專用工具，可簡化建立、部署和調試作業的工作。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Azure App Service 檔](/azure/app-service/)
- [快速入門](/azure/app-service/app-service-web-get-started-dotnet)
- [範例](/azure/app-service/samples-cli)
- [教學課程](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 會管理裝載的 Kubernetes 環境。 AKS 可快速且輕鬆地部署和管理容器化的應用程式，而不需要容器協調流程專業知識。 它也可以消除進行中作業和維護的負擔。 AKS 會視需要布建、升級及調整資源，而不需讓應用程式離線。

**主要功能**
- 輕鬆地將現有的應用程式遷移至容器，並在 AKS 內執行。
- 簡化微服務型應用程式的部署和管理。
- 保護 DevOps 的 AKS，以達到速度和安全性之間的平衡，並更快速地提供程式碼。
- 藉由使用 AKS 和 Azure 容器實例在幾秒內啟動的容器實例中布建 pod，輕鬆地調整規模。
- 視需要部署及管理 IoT 裝置。
- 使用 TensorFlow 和 KubeFlow 等工具來定型機器學習模型。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Azure Kubernetes Service 檔](/azure/aks/)
- [快速入門](/azure/aks/kubernetes-walkthrough-portal)
- [教學課程](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
對於可在隔離容器中運作的任何案例，例如簡單的應用程式、工作自動化和組建作業， [Azure 容器實例](https://azure.microsoft.com/services/container-instances/)是絕佳的解決方案。 快速開發應用程式，而不需要管理 Vm。

**主要功能**
- 因為容器實例可以在幾秒內啟動 Azure 中的容器，而不需要布建和管理 Vm，所以快速啟動時間。
- 公用 IP 連線能力和自訂 DNS 名稱。
- 可確保您的應用程式在容器中會如同在 VM 中隔離的虛擬機器層級安全性。
- 藉由允許 CPU 核心和記憶體的確切規格，來達到最佳使用率的自訂大小。 您可根據所需的數量付費並採用以秒計費方式，因此能夠根據實際的需求來微調您的費用。
- 持續性儲存體，可取得並保存狀態。 容器實例提供 Azure 檔案儲存體共用的直接裝載。
- 使用相同 API 排程的 Linux 和 Windows 容器。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Azure 容器實例檔](/azure/container-instances/)
- [快速入門](/azure/container-instances/container-instances-quickstart-portal)
- [範例](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [教學課程](/azure/container-instances/container-instances-tutorial-prepare-app)