---
title: 從基本內部升級到標準內部 - Azure 負載等化器
description: 本文介紹如何將 Azure 內部負載等化器從基本 SKU 升級到標準 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: c2c909d8ef2be982d4dd4a70b5f35d03e8e71418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659963"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升級 Azure 內部負載等化器 - 無需出站連接
[Azure 標準負載等化器](load-balancer-overview.md)通過區域冗余提供了豐富的功能集和高可用性。 要瞭解有關負載等化器 SKU 的更多，請參閱[比較表](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)。

升級分為兩個階段：

1. 遷移配置
2. 將 VM 添加到標準負載等化器的後端池

本文介紹配置遷移。 將 VM 添加到後端池可能因特定環境而異。 然而，[提供了](#add-vms-to-backend-pools-of-standard-load-balancer)一些高層次的一般性建議。

## <a name="upgrade-overview"></a>升級概觀

Azure PowerShell 腳本可用於執行以下操作：

* 在指定的位置創建標準內部 SKU 負載等化器。 請注意，標準內部負載等化器不會提供[出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。
* 將基本 SKU 負載等化器的配置無縫複製到新創建的標準負載等化器。

### <a name="caveatslimitations"></a>警告\限制

* 腳本僅支援不需要出站連接的內部負載等化器升級。 如果您需要某些 VM 的[出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)，請參閱[此頁面](upgrade-InternalBasic-To-PublicStandard.md)獲得說明。 
* 標準負載等化器具有新的公共位址。 不可能將與現有基本負載等化器關聯的 IP 位址無縫移動到標準負載等化器，因為它們具有不同的 SKU。
* 如果標準負載等化器是在不同區域中創建的，您將無法將舊區域中現有的 VM 與新創建的標準負載等化器相關聯。 要解決此限制，請確保在新區域中創建新的 VM。
* 如果您的負載等化器沒有任何前端 IP 配置或後端池，則運行腳本時可能會遇到錯誤。 請確保它們不為空。

## <a name="download-the-script"></a>下載腳本

從[PowerShell 庫](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)下載遷移腳本。
## <a name="use-the-script"></a>使用腳本

根據您的本地 PowerShell 環境設置和首選項，有兩個選項可供您使用：

* 如果未安裝 Azure Az 模組，或者不介意卸載 Azure Az 模組，則最佳選項是使用`Install-Script`選項運行腳本。
* 如果需要保留 Azure Az 模組，最好下載腳本並直接運行它。

要確定是否安裝了 Azure Az 模組，請`Get-InstalledModule -Name az`運行 。 如果看不到任何已安裝的 Az 模組，則可以使用 方法`Install-Script`。

### <a name="install-using-the-install-script-method"></a>使用安裝腳本方法進行安裝

要使用此選項，不得在電腦上安裝 Azure Az 模組。 如果安裝了它們，則以下命令將顯示錯誤。 您可以卸載 Azure Az 模組，也可以使用另一個選項手動下載腳本並運行腳本。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzureILBUpgrade`

此命令還安裝所需的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用腳本安裝

如果確實安裝了某些 Azure Az 模組，但無法卸載它們（或不想卸載它們），則可以使用腳本下載連結中的 **"手動下載**"選項卡手動下載腳本。 腳本下載為原始 nupkg 檔。 要從此 nupkg 檔安裝腳本，請參閱[手動包下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 用於`Connect-AzAccount`連接到 Azure。

1. 用於`Import-Module Az`導入 Az 模組。

1. 檢查所需的參數：

   * **rgName： [字串]： 必需**– 這是現有基本負載等化器和新的標準負載等化器的資源組。 要查找此字串值，請導航到 Azure 門戶，選擇基本負載等化器源，然後按一下負載等化器的 **"概述**"。 資源組位於該頁上。
   * **舊 LB 名稱： [字串]： 必需**– 這是要升級的現有基本平衡器的名稱。 
   * **新定位： [String]： 必需**– 這是將創建標準負載等化器的位置。 建議將所選基本負載等化器的相同位置繼承到標準負載平衡器，以便更好地與其他現有資源關聯。
   * **新 LB 名稱： [字串]： 必需**– 這是要創建的標準負載等化器的名稱。
1. 使用適當的參數運行腳本。 可能需要五到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>將 VM 添加到標準負載等化器的後端池

首先，仔細檢查腳本成功創建了新的標準內部負載等化器，其精確配置從基本內部負載等化器遷移過來。 可以從 Azure 門戶驗證此情況。

請務必通過標準負載等化器發送少量流量作為手動測試。
  
以下是有關如何將 VM 添加到新創建的標準內部負載等化器的後端池的一些方案，以及我們對每個方案的建議：

* **將現有 VM 從舊基本內部負載等化器的後端池移動到新創建的標準內部負載等化器的後端池**。
    1. 若要進行本快速入門中的工作，請登入 [Azure 入口網站](https://portal.azure.com)。
 
    1. 選擇左側功能表上**的所有資源**，然後從資源清單中選擇**新創建的標準負載等化器**。
   
    1. 在 [設定]**** 底下選取 [後端集區]****。
   
    1. 選擇與基本負載等化器的後端池匹配的後端池，選擇以下值： 
      - **虛擬機器**：從基本負載等化器的匹配後端池下拉並選擇 VM。
    1. 選取 [儲存]****。
    >[!NOTE]
    >對於具有公共 IP 的 VM，您需要首先創建標準 IP 位址，而該位址不保證相同的 IP 位址。 取消 VM 與基本 IP 的關聯，並將其與新創建的標準 IP 位址相關聯。 然後，您將能夠按照說明將 VM 添加到標準負載等化器的後端池中。 

* **創建新 VM 以添加到新創建的標準內部負載等化器的後端池**。
    * 有關如何創建 VM 並將其與標準負載等化器關聯的更多說明[，請參閱此處](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)。

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 腳本是否有任何限制，可以將配置從 v1 遷移到 v2？

是。 請參閱[警告/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 腳本是否還會將流量從基本負載等化器切換到新創建的標準負載等化器？

否。 Azure PowerShell 腳本僅遷移配置。 實際流量遷移是您的責任，也是您的控制。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我遇到了使用此腳本的一些問題。 如何獲得説明？
  
您可以向slbupgradesupport@microsoft.com、打開 Azure 支援案例的電子郵件，也可以同時執行此操作。

## <a name="next-steps"></a>後續步驟

[瞭解標準負載等化器](load-balancer-overview.md)
