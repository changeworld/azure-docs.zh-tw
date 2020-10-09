---
title: 更新 Visual Studio 的範本部署腳本以使用 Az PowerShell
description: 將 Visual Studio 範本部署腳本從 AzureRM 更新為 Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963864"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>更新 Visual Studio 範本部署腳本以使用 Az PowerShell 模組

Visual Studio 16.4 支援在範本部署腳本中使用 Az PowerShell 模組。 不過，Visual Studio 不會自動安裝該模組。 若要使用 Az 模組，您必須採取四個步驟：

1. [卸載 AzureRM 模組](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [安裝 Az 模組](/powershell/azure/install-az-ps)
1. 將 Visual Studio 更新至 16.4
1. 更新您專案中的部署指令碼。

## <a name="update-visual-studio-to-164"></a>將 Visual Studio 更新至 16.4

將您的 Visual Studio 安裝更新至16.4 版或更新版本。 在升級期間，請確定未核取 Azure PowerShell 元件。 由於您已透過資源庫安裝 Az 模組，因此不會想要重新安裝 AzureRM 模組。

如果您已升級至16.4，並已核取 Azure PowerShell 元件，您可以執行 Visual Studio 安裝程式將其卸載。 請勿在 Azure 工作負載或 [個別元件] 頁面上選取 Azure PowerShell 元件。

## <a name="update-the-deployment-script-in-your-project"></a>更新您專案中的部署指令碼

將所有出現的 'AzureRm’ 字串取代為您部署指令碼中的 'Az'。 您可以參考此 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) 中的修訂以查看變更。 如需將腳本升級至 Az 模組的詳細資訊，請參閱 [將 Azure PowerShell 從 AzureRM 遷移至 az](/powershell/azure/migrate-from-azurerm-to-az)。

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 Visual Studio 專案，請參閱 [透過 Visual Studio 建立和部署 Azure 資源群組專案](create-visual-studio-deployment-project.md)。
