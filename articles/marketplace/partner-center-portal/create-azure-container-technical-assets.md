---
title: Azure 容器供應項目的技術概念 - Microsoft 商業市集
description: 協助您在 Azure Marketplace 上設定容器供應項目的技術資源和指導方針。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700975"
---
# <a name="create-an-azure-container-offer"></a>建立 Azure 容器供應項目

本文提供技術資源和建議，協助您在 Azure Marketplace 上建立容器供應項目。

## <a name="before-you-begin"></a>開始之前

請參閱 [Azure 容器執行個體文件](https://docs.microsoft.com/azure/container-instances)，取得快速入門、教學課程和範例。

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

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

建議您將這些工具新增至開發環境：

- [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 擴充功能：[Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
  - 擴充功能：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) \(英文\)
  - 擴充功能：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

在 [Azure 開發人員工具](https://azure.microsoft.com/)頁面中檢閱可用工具。 如果您使用的是 Visual Studio，請檢閱 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 中提供的工具。

## <a name="create-the-container-image"></a>建立容器映像

如需詳細資訊，請參閱下列教學課程：

- [教學課程：建立容器影像以部署至 Azure 容器執行個體](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [教學課程：使用 Azure Container Registry 工作在雲端中建置和部署容器映像](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)。

## <a name="next-steps"></a>後續步驟

- [建立容器供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)。
