---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018139"
---
安裝最新版的 Azure 服務管理 (SM) PowerShell 模組和 ExpressRoute 模組。 您不能使用 Azure CloudShell 環境來執行 SM 模組。

1. 請使用[安裝服務管理模組](/powershell/azure/servicemanagement/install-azure-ps)一文中的指示來安裝 Azure 服務管理模組。 如果您已安裝 Az 或 RM 模組，請務必使用 '-AllowClobber'。
2. 匯入已安裝的模組。 使用下列範例時，請調整路徑以反映已安裝 PowerShell 模組的位置和版本。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. 若要登入您的 Azure 帳戶，請以提高的權限開啟 PowerShell 主控台並連線到您的帳戶。 使用下列範例可協助您使用服務管理模組進行連線：

   ```powershell
   Add-AzureAccount
   ```