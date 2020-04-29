---
title: 建立 Azure VM 技術資產
description: 本文說明在 Azure Marketplace 上設定容器供應專案的步驟和需求。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730630"
---
# <a name="create-an-azure-vm-technical-asset"></a>建立 Azure VM 技術資產

> [!IMPORTANT]
> 我們正在將您的 Azure 容器供應專案管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請遵循[準備您的容器技術資產](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets)以進行 Cloud Partner 入口網站管理您的供應專案中的指示。

本文說明在 Azure Marketplace 上設定容器供應專案的步驟和需求。

## <a name="before-you-begin"></a>開始之前

如需快速入門、教學課程和範例，請參閱[Azure 容器實例](https://docs.microsoft.com/azure/container-instances)。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建立及測試這些資產需要一些時間，而且需要 Azure 平臺和用來建立供應專案之技術的技術知識。

除了您的解決方案網域以外，您的工程小組也應具備下列 Microsoft 技術的相關知識：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和[azure 網路](https://azure.microsoft.com/services/?filter=networking)的使用知識
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
- [JSON](https://www.json.org/)的使用知識。

## <a name="suggested-tools"></a>建議的工具

選擇下列其中一個或兩個腳本環境，以協助管理您的容器映射：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

建議您將這些工具新增至您的開發環境：

- [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 延伸模組：[修飾 JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

請參閱[Azure 開發人員工具](https://azure.microsoft.com/)頁面上的可用工具。 如果您使用的是 Visual Studio，請參閱[Visual Studio Marketplace](https://marketplace.visualstudio.com/)中提供的工具。

## <a name="create-the-container-image"></a>建立容器映像

如需詳細資訊，請參閱下列教學課程：

- [教學課程：建立容器映射以部署至 Azure 容器實例](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [教學課程：使用 Azure Container Registry 工作在雲端中建立及部署容器映射](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)。

## <a name="next-step"></a>後續步驟

- [建立您的容器供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)專案。
