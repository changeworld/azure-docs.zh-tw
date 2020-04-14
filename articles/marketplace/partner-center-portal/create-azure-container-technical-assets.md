---
title: 準備 Azure 容器技術資產
description: 本文介紹了在 Azure 應用商店上配置容器產品提供的步驟和要求。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 62c77681accb19183b5e8aa3ae8d80c225b2d284
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266015"
---
# <a name="prepare-your-azure-container-technical-assets"></a>準備 Azure 容器技術資產

> [!IMPORTANT]
> 我們將 Azure 容器產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照雲合作夥伴門戶[準備容器技術資產](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets)以管理優惠的說明進行操作。

本文介紹了在 Azure 應用商店上配置容器產品提供的步驟和要求。

## <a name="before-you-begin"></a>開始之前

有關快速入門、教程和範例,請參閱[Azure 容器實例](https://docs.microsoft.com/azure/container-instances)。

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、構建和測試這些資產需要時間,需要對 Azure 平臺和用於構建產品/服務的技術進行技術知識。

除了解決方案領域之外,您的工程團隊還應瞭解以下 Microsoft 技術:

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)[、Azure 儲存](https://azure.microsoft.com/services/?filter=storage)和 Azure[網路](https://azure.microsoft.com/services/?filter=networking)的工作知識
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
- [JSON](https://www.json.org/)的工作知識 。

## <a name="suggested-tools"></a>建議的工具

選擇以下文稿環境的一個或兩個,以説明管理容器映射:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

我們建議將這些工具加入式開發環境中:

- [Azure 儲存體總管](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 延伸:[預化 JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

檢視[Azure 開發人員工具](https://azure.microsoft.com/)頁面上的可用工具。 如果您使用的是可視化工作室,請查看[可視化工作室應用商店](https://marketplace.visualstudio.com/)中可用的工具。

## <a name="create-the-container-image"></a>建立容器映像

有關詳細資訊,請參閱以下教程:

- [教學:建立容器映像以部署到 Azure 容器實體](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [教學:使用 Azure 容器註冊表工作在雲端中建譯及部署容器映像](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)。

## <a name="next-step"></a>後續步驟

- [建立您的容器產品/服務](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)。
