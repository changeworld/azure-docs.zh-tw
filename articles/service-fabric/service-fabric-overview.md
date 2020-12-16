---
title: Azure Service Fabric 概觀
description: Service Fabric 是一種分散式系統平台，用於建置可調整、可靠且輕鬆管理的微服務。
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028626"
---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric 概觀

Azure Service Fabric 是一個[分散式系統平台](#container-orchestration)，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務與容器。 Service Fabric 也解決了[開發及管理](#application-lifecycle-management)雲端原生應用程式時所面臨的重大挑戰。

Service Fabric 的主要差異在於，其非常著重在建置具狀態服務。 您可以使用 Service Fabric [程式設計模型](#stateless-and-stateful-microservices)，或執行以任何語言或程式碼撰寫的容器化具狀態服務。 除了 Azure 以外，您還可以隨處建立 [Service Fabric 叢集](#any-os-any-cloud)，包括 Windows Server 和 Linux 內部部署以及其他公用雲端。

![Service Fabric 平台提供生命週期管理、可用性、協調流程、程式設計模型、健康情況和監視、開發和操作工具，以及自動調整--在 Azure 中、內部部署、在其他雲端中，以及在您的開發電腦上][Image1]

Service Fabric 提供技術支援給現今許多 Microsoft 服務，包括 Azure SQL Database、Azure Cosmos DB、Cortana、Microsoft Power BI、Microsoft Intune、「Azure 事件中樞」、「Azure IoT 中樞」、Dynamics 365、「商務用 Skype」以及許多核心 Azure 服務。

## <a name="container-orchestration"></a>容器協調流程

Service Fabric 是 Microsoft 的[容器協調器](service-fabric-cluster-resource-manager-introduction.md)，用於跨機器叢集部署和管理微服務，受益於大規模執行 Microsoft 服務的經驗教訓。 以每部機器上數百或數千個應用程式或容器的高密度方式，Service Fabric 可以快速部署應用程式。 使用 Service Fabric 時，您可以在相同應用程式中同時混合容器中的服務和處理序中的服務。

[深入了解 Service Fabric](service-fabric-content-roadmap.md) 核心概念、程式設計模型、應用程式生命週期、測試、叢集及健康情況監視。

## <a name="stateless-and-stateful-microservices"></a>無狀態微服務和具狀態微服務

Service Fabric 提供精密、輕量的執行階段，可支援無狀態和具狀態的微服務。 Service Fabric 的主要區別在於其強力支援建置具狀態服務，不論是使用 Service Fabric [內建的程式設計模型](service-fabric-choose-framework.md)，還是使用容器化具狀態服務來建置。

深入了解受益於 Service Fabric 具狀態服務的[應用程式案例](service-fabric-application-scenarios.md)。

## <a name="application-lifecycle-management"></a>應用程式生命週期管理

Service Fabric 支援完整應用程式生命週期和雲端應用程式的 CI/CD，包括容器：開發到部署、每日監視、管理和維護，再到最終的解除委任。 Service Fabric 已與 CI/CD 工具 (例如 [Azure Pipelines](https://www.visualstudio.com/team-services/)、[Jenkins](https://jenkins.io/index.html) 及 [Octopus Deploy](https://octopus.com/)) 整合，並且可與任何其他常用的 CI/CD 工具搭配使用。

如需應用程式生命週期管理的詳細資訊，請參閱[應用程式生命週期](service-fabric-application-lifecycle.md)。 如需將現有的應用程式部署至 Service Fabric，請參閱[部署來賓可執行檔](service-fabric-deploy-existing-app.md)。

## <a name="any-os-any-cloud"></a>任何 OS、任何雲端

您可以在許多環境 (包括 [Azure 或內部部署](service-fabric-deploy-anywhere.md)、[Windows Server 或 Linux](service-fabric-linux-windows-differences.md) 上) 建立 Service Fabric 的叢集。 您甚至可以在其他公用雲端上建立叢集。 Service Fabric SDK 中的開發環境與生產環境相同，不涉及任何模擬器。 換句話說，在本機開發叢集上執行的項目，也是部署到其他環境中叢集的項目。

對於 [Windows 開發](service-fabric-get-started.md)，Service Fabric.NET SDK 與 Visual Studio 和 PowerShell 整合。 對於 [Linux 開發](service-fabric-get-started-linux.md)，Service Fabric Java SDK 與 Eclipse 整合，Yeoman 用於為 Java、.NET Core 和容器應用程式產生範本。

## <a name="compliance"></a>法規遵循

Azure Service Fabric 資源提供者可在所有 Azure 區域中使用，並且符合所有 Azure 合規性憑證的規範，包括：SOC、ISO、PCI DSS、HIPAA 及 GDPR。 如需完整清單，請參閱 [Microsoft 合規性供應項目](https://www.microsoft.com/trustcenter/compliance/complianceofferings)。

## <a name="next-steps"></a>後續步驟

在 Azure Service Fabric 上建立並部署第一個應用程式：

> [!div class="nextstepaction"]
> [Service Fabric 快速入門][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
