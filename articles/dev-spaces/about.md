---
title: 什麼是 Azure Dev Spaces？
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: 了解 Azure Dev Spaces 可為 Azure Kubernetes Service 叢集中的小組提供快速、反覆的 Kubernetes 開發體驗。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 691845cd888e4d24c1144f2805402a3baf14a86e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006959"
---
# <a name="what-is-azure-dev-spaces"></a>什麼是 Azure Dev Spaces？

Azure Dev Spaces 可為 Azure Kubernetes Service (AKS) 叢集中的小組提供快速、反覆的 Kubernetes 開發體驗。 Azure Dev Spaces 也可讓您以最少量的開發機器設定在 AKS 中偵測及測試應用程式的所有元件，而不需要複寫或模擬相依性。

![此圖顯示兩個獨立開發的應用程式版本。 這兩個版本會在 Azure Dev Spaces 開發環境中合而為一。](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure 開發人員空間如何簡化 Kubernetes 開發

Azure Dev Spaces 可讓小組成員直接使用他們在 AKS 中執行的整個微服務架構或應用程式，而協助他們全心投入其微服務應用程式的開發和快速的反覆執行。 Azure Dev Spaces 也可讓您單獨更新微服務架構的某些部分，而不會影響到其餘的 AKS 叢集或其他開發人員。 Azure Dev Spaces 適用於較低層級的開發和測試環境中的開發和測試，而不適合在生產 AKS 叢集上執行。

由於小組成員可以直接在 AKS 中使用整個應用程式和共同作業，因此 Azure Dev Spaces 能夠：

* 盡可能簡化本機電腦設定
* 縮短小組中的新進開發人員所需的設定時間
* 透過更快速的反覆執行，加快小組的作業速度
* 減少多餘的開發和整合環境數目，因為小組成員可以共用叢集
* 免除複寫或模擬相依性的需求
* 增進開發小組及其合作小組 (例如 DevOps 小組) 之間的共同作業效率

Azure Dev Spaces 提供可為您的專案產生 Docker 和 Kubernetes 資產的工具。 此工具可讓您輕鬆地將新的和現有的應用程式新增至開發人員空間和其他 AKS 叢集。

如需 Azure Dev Spaces 運作方式的詳細資訊，請參閱 [Azure Dev Spaces 的運作和設定方式][how-dev-spaces-works]。

## <a name="supported-regions-and-configurations"></a>支援的區域和組態

在[某些區域][supported-regions]中，只有 AKS 叢集會支援 Azure Dev Spaces。 Azure Dev Spaces 支援使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Visual Studio Code](https://code.visualstudio.com/download) 搭配在 Linux、macOS 或 Windows 8 或更新版本上安裝的 [Azure Dev Spaces 擴充功能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)，以在 AKS 上建置和執行應用程式。 也支援使用 [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)，搭配在 Windows 上安裝的 Azure 開發工作負載。

## <a name="next-steps"></a>後續步驟

透過[小組開發快速入門][team-development-quickstart]，深入了解小組成員如何使用 Azure Dev Spaces 進行快速的反覆式開發。

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
