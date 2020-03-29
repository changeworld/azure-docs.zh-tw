---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926234"
---
安裝最新版的 Azure 服務管理 (SM) PowerShell 模組和 ExpressRoute 模組。 不能使用 Azure 雲殼環境運行 SM 模組。

1. 使用["安裝服務管理"模組](/powershell/azure/servicemanagement/install-azure-ps)一文中的說明安裝 Azure 服務管理模組。 如果已安裝 Az 或 RM 模組，請確保使用"-允許 Clobber"。
2. 導入已安裝的模組。 使用以下示例時，調整路徑以反映已安裝的 PowerShell 模組的位置和版本。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. 要登錄到 Azure 帳戶，請打開具有提升許可權的 PowerShell 主控台並連接到您的帳戶。 使用以下示例説明您使用服務管理模組進行連接：

   ```powershell
   Add-AzureAccount
   ```