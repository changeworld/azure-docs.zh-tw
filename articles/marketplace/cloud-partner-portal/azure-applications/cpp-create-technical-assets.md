---
title: 創建 Azure 應用程式技術資產 |Azure 應用商店
description: 建立 Azure 應用程式供應項目的技術資產。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285260"
---
# <a name="prepare-your-azure-application-technical-assets"></a>準備 Azure 應用程式技術資產

本文會介紹可用來準備 Azure 應用程式供應項目技術資產的資源。

## <a name="before-you-begin"></a>開始之前

請觀看下列影片[建置 Azure Marketplace 的解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)，其會概述如何撰寫 Azure Resource Manager 範本以定義 Azure 應用程式解決方案，以及之後如何將應用程式供應項目發佈至 Azure Marketplace。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


請參閱以下 Azure 應用程式文件，其中提供快速入門、教學課程和範例。

- [了解 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- 快速入門：

  - [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
  - [發佈應用程式定義](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [部署服務目錄應用](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- 教學課程：

  - [建立定義檔案](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [發佈市集應用程式](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - 範例：

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure 電源外殼](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [受控應用程式解決方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。

您的工程小組應具備下列 Microsoft 技術的知識：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)[、Azure 存儲](https://azure.microsoft.com/services/?filter=storage)和 Azure[網路](https://azure.microsoft.com/services/?filter=networking)的工作知識
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
- [JSON](https://www.json.org/)的工作知識

## <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 Azure 應用程式：

- [Azure 電源外殼](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

建議您將下列工具新增至開發環境：

- [Azure 存儲資源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/) \(英文\)：

  - 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 延伸模組：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

也建議您至 [Azure 開發人員工具](https://azure.microsoft.com/tools/) 頁面查看可用的工具，若您使用 Visual Studio，也請查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="next-steps"></a>後續步驟

[建立 Azure 應用程式供應項目](./cpp-create-offer.md)

