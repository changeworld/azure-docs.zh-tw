---
title: 更新視覺化工作室的範本部署腳本以使用 Az PowerShell
description: 將視覺化工作室範本部署腳本從 AzureRM 更新到 Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963864"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>更新視覺化工作室範本部署腳本以使用 Az PowerShell 模組

Visual Studio 16.4 支援在範本部署腳本中使用 Az PowerShell 模組。 但是，Visual Studio 不會自動安裝該模組。 要使用 Az 模組，您需要執行四個步驟：

1. [卸載 AzureRM 模組](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [安裝 Az 模組](/powershell/azure/install-az-ps)
1. 將 Visual Studio 更新至 16.4
1. 更新您專案中的部署指令碼。

## <a name="update-visual-studio-to-164"></a>將 Visual Studio 更新至 16.4

將視覺化工作室的安裝更新為版本 16.4 或更高版本。 在升級期間，請確保未選中 Azure PowerShell 元件。 由於通過庫安裝了 Az 模組，因此不希望重新安裝 AzureRM 模組。

如果已升級到 16.4 並且已選中 Azure PowerShell 元件，則可以通過運行 Visual Studio 安裝程式卸載它。 不要在 Azure 工作負荷或各個元件頁上選擇 Azure PowerShell 元件。

## <a name="update-the-deployment-script-in-your-project"></a>更新您專案中的部署指令碼

將所有出現的 'AzureRm’ 字串取代為您部署指令碼中的 'Az'。 您可以參考此 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) 中的修訂以查看變更。 有關將腳本升級到 Az 模組的詳細資訊，請參閱[將 Azure PowerShell 從 AzureRM 遷移到 Az](/powershell/azure/migrate-from-azurerm-to-az)。

## <a name="next-steps"></a>後續步驟

要瞭解如何使用視覺化工作室專案，請參閱通過[Visual Studio 創建和部署 Azure 資源組專案](create-visual-studio-deployment-project.md)。
