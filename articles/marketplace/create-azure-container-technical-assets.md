---
title: 準備您的 Azure 容器技術資產
description: 協助您在 Azure Marketplace 上設定容器供應項目的技術資源和指導方針。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459329"
---
# <a name="prepare-your-azure-container-technical-assets"></a>準備您的 Azure 容器技術資產

本文提供技術資源和建議，協助您在 Azure Marketplace 上建立容器供應項目。

## <a name="before-you-begin"></a>開始之前

請參閱 [Azure 容器執行個體文件](../container-instances/index.yml)，取得快速入門、教學課程和範例。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。

除了解決方案領域外，工程小組還應具備下列 Microsoft 技術的知識：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
- 具備 [JSON](https://www.json.org/) 的運用知識。

## <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理容器映像：

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/)。

建議您將這些工具新增至開發環境：

- [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 擴充功能：[Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
  - 擴充功能：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) \(英文\)
  - 擴充功能：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

在 [Azure 開發人員工具](https://azure.microsoft.com/)頁面中檢閱可用工具。 如果您使用的是 Visual Studio，請檢閱 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 中提供的工具。

## <a name="create-the-container-image"></a>建立容器映像

您無法從內部部署登錄將映射部署至 Azure 容器實例。

- 如果您的本機登錄中已經有正常運作的容器，請建立 Azure 登錄，並將您的容器映射上傳至 Azure Container Registry。 若要深入瞭解，請參閱 [教學課程：使用 Azure Container Registry 工作在雲端中建立和部署容器映射](../container-registry/container-registry-tutorial-quick-task.md)。

- 如果尚無容器映射，且需要將現有的應用程式，或建立新的容器型應用程式，請從 GitHub 複製應用程式原始程式碼，從應用程式來源建立容器映射，然後在本機 Docker 環境中測試映射。 若要深入瞭解，請參閱 [教學課程：建立容器映射以部署至 Azure 容器實例](../container-instances/container-instances-tutorial-prepare-app.md)。

## <a name="next-steps"></a>後續步驟

- [建立您的容器供應專案](create-azure-container-offer.md)