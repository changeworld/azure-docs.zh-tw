---
title: 從基本內部升級到標準內部 - Azure 負載均衡器
description: 本文介紹如何將 Azure 內部負載均衡器從基本 SKU 升級到標準 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770378"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升級 Azure 內部負載均衡器 ─無需出站連線
[Azure 標準負載均衡器](load-balancer-overview.md)通過區域冗餘提供了豐富的功能集和高可用性。 要瞭解有關負載均衡器 SKU 的更多,請參閱[比較表](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)。

本文介紹了一個 PowerShell 腳本,該腳本創建與基本負載均衡器配置相同的標準負載均衡器,同時將流量從基本負載均衡器遷移到標準負載均衡器。

## <a name="upgrade-overview"></a>升級概觀

Azure PowerShell 文稿可用於執行以下操作:

* 在指定的位置創建標準內部 SKU 負載均衡器。 請注意,標準內部負載均衡器不會提供[出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。
* 將基本 SKU 負載均衡器的配置無縫複製到新創建的標準負載均衡器。
* 無縫地將專用 IP 從基本負載均衡器移動到新創建的標準負載均衡器。
* 將 VM 從基本負載均衡器的後端池無縫移動到標準負載均衡器的後端池

### <a name="caveatslimitations"></a>警告\限制

* 腳本僅支援不需要出站連接的內部負載均衡器升級。 如果您需要某些 VM 的[出站連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections),請參閱[此頁面](upgrade-InternalBasic-To-PublicStandard.md)獲得說明。 
* 如果標準負載均衡器是在不同區域中創建的,您將無法將舊區域中現有的 VM 與新創建的標準負載均衡器相關聯。 要解決此限制,請確保在新區域中創建新的 VM。
* 如果您的負載均衡器沒有任何前端 IP 配置或後端池,則執行文稿時可能會遇到錯誤。 確保它們不為空。

## <a name="download-the-script"></a>下載文稿

從[PowerShell 庫](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0)下載遷移腳本。
## <a name="use-the-script"></a>使用文稿

從您的本地 PowerShell 環境設定與偏好設定,有兩個選項可供您使用:

* 如果未安裝 Azure Az 模組,或者不介意卸載 Azure Az 模組`Install-Script`,則最佳選項是使用 選項運行腳本。
* 如果需要保留 Azure Az 模組,最好下載腳本並直接運行它。

要確定是否安裝 Azure Az 模組`Get-InstalledModule -Name az`,請執行 。 如果看不到任何已安裝的 Az 模組,則可以`Install-Script`使用方法 。

### <a name="install-using-the-install-script-method"></a>使用安裝文稿方法進行安裝

要使用此選項,不得在電腦上安裝 Azure Az 模組。 如果安裝了它們,則以下命令將顯示錯誤。 您可以卸載 Azure Az 模組,也可以使用另一個選項手動下載腳本並運行文稿。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzureILBUpgrade`

此命令還安裝所需的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用文稿安裝

如果確實安裝了某些 Azure Az 模組,但無法卸載它們(或不想卸載它們),則可以使用腳本下載連結中的 **「手動下載**」選項卡手動下載腳本。 腳本下載為原始 nupkg 檔。 要從此 nupkg 檔案安裝文稿,請參閱[手動套件下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 用於`Connect-AzAccount`連接到 Azure。

1. 用於`Import-Module Az`導入 Az 模組。

1. 檢查所需的參數:

   * **rgName: [字串]: 必需**– 這是現有基本負載均衡器和新的標準負載均衡器的資源組。 要查找此字串值,請導航到 Azure 門戶,選擇基本負載均衡器源,然後單擊負載均衡器的 **「概述**」。 資源組位於該頁上。
   * **舊 LB 名稱: [字串]: 必需**– 這是要升級的現有基本平衡器的名稱。 
   * **新定位: [String]: 必需**– 這是將創建標準負載均衡器的位置。 建議將所選基本負載均衡器的相同位置繼承到標準負載平衡器,以便更好地與其他現有資源關聯。
   * **新 LB 名稱: [字串]: 必需**– 這是要創建的標準負載均衡器的名稱。
1. 使用適當的參數運行腳本。 可能需要五到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 文本是否有任何限制,可以將配置從 v1 遷移到 v2?

是。 請參考[警告 / 限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 腳本是否還會將流量從基本負載均衡器切換到新創建的標準負載均衡器?

是的,它遷移流量。 如果要親自遷移流量,請使用[此腳本](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0),該腳本不會為您移動 VM。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我遇到了使用此腳本的一些問題。 如何獲得説明?
  
您可以向slbupgradesupport@microsoft.com、打開 Azure 支援案例的電子郵件,也可以同時執行此操作。

## <a name="next-steps"></a>後續步驟

[瞭解標準負載均衡器](load-balancer-overview.md)
