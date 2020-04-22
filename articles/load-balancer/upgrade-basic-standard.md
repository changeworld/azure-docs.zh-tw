---
title: 從基本公共升級到標準公共 - Azure 負載均衡器
description: 本文介紹如何將 Azure 公共負載均衡器從基本 SKU 升級到標準 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a2d6f41756d87e43ac7db9e6a8670c453920c834
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770359"
---
# <a name="upgrade-azure-public-load-balancer"></a>升級 Azure 公共負載均衡器
[Azure 標準負載均衡器](load-balancer-overview.md)通過區域冗餘提供了豐富的功能集和高可用性。 要瞭解有關負載均衡器 SKU 的更多,請參閱[比較表](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)。

升級分為三個階段:

1. 移轉設定
2. 將 VM 新增到標準負載均衡器的後端池

本文介紹配置遷移。 將 VM 添加到後端池可能因特定環境而異。 然而,[提供了](#add-vms-to-backend-pools-of-standard-load-balancer)一些高層次的一般性建議。

## <a name="upgrade-overview"></a>升級概觀

Azure PowerShell 文稿可用於執行以下操作:

* 在指定的資源組中和位置中創建標準 SKU 負載均衡器。
* 將基本 SKU 負載均衡器的配置無縫複製到新創建的標準負載均衡器。
* 創建啟用出站連接的預設出站規則。

### <a name="caveatslimitations"></a>警告\限制

* 腳本僅支援公共負載均衡器升級。 有關內部基本負載均衡器升級,請參閱[此頁面](https://docs.microsoft.com/azure/load-balancer/upgrade-basicinternal-standard)以獲得說明。
* 標準負載均衡器具有新的公共位址。 不可能將與現有基本負載均衡器關聯的 IP 位址無縫移動到標準負載均衡器,因為它們具有不同的 SKU。
* 如果標準負載均衡器是在不同區域中創建的,您將無法將舊區域中現有的 VM 與新創建的標準負載均衡器相關聯。 要解決此限制,請確保在新區域中創建新的 VM。
* 如果您的負載均衡器沒有任何前端 IP 配置或後端池,則執行文稿時可能會遇到錯誤。 請確保它們不為空。

## <a name="download-the-script"></a>下載文稿

從[PowerShell 庫](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0)下載遷移腳本。
## <a name="use-the-script"></a>使用文稿

從您的本地 PowerShell 環境設定與偏好設定,有兩個選項可供您使用:

* 如果未安裝 Azure Az 模組,或者不介意卸載 Azure Az 模組`Install-Script`,則最佳選項是使用 選項運行腳本。
* 如果需要保留 Azure Az 模組,最好下載腳本並直接運行它。

要確定是否安裝 Azure Az 模組`Get-InstalledModule -Name az`,請執行 。 如果看不到任何已安裝的 Az 模組,則可以`Install-Script`使用方法 。

### <a name="install-using-the-install-script-method"></a>使用安裝文稿方法進行安裝

要使用此選項,不得在電腦上安裝 Azure Az 模組。 如果安裝了它們,則以下命令將顯示錯誤。 您可以卸載 Azure Az 模組,也可以使用另一個選項手動下載腳本並運行文稿。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzurePublicLBUpgrade`

此命令還安裝所需的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用文稿安裝

如果確實安裝了某些 Azure Az 模組,但無法卸載它們(或不想卸載它們),則可以使用腳本下載連結中的 **「手動下載**」選項卡手動下載腳本。 腳本下載為原始 nupkg 檔。 要從此 nupkg 檔案安裝文稿,請參閱[手動套件下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 用於`Connect-AzAccount`連接到 Azure。

1. 用於`Import-Module Az`導入 Az 模組。

1. 檢查所需的參數:

   * **舊 RgName: [String]: 必需**– 這是要升級的現有基本負載均衡器的資源組。 要查找此字串值,請導航到 Azure 門戶,選擇基本負載均衡器源,然後單擊負載均衡器的 **「概述**」。 資源組位於該頁上。
   * **舊 LB 名稱: [字串]: 必需**– 這是要升級的現有基本平衡器的名稱。 
   * **NewrgName: [String]: 必需**– 這是將在其中創建標準負載均衡器的資源組。 它可以是一個新的資源組,也可以是現有的資源組。 如果選擇現有資源組,請注意負載均衡器的名稱必須在資源組中是唯一的。 
   * **新定位: [String]: 必需**– 這是將創建標準負載均衡器的位置。 建議將所選基本負載均衡器的相同位置繼承到標準負載平衡器,以便更好地與其他現有資源關聯。
   * **新 LB 名稱: [字串]: 必需**– 這是要創建的標準負載均衡器的名稱。
1. 使用適當的參數運行腳本。 可能需要五到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>將 VM 新增到標準負載均衡器的後端池

首先,仔細檢查腳本成功創建了新的標準公共負載均衡器,其確切配置是從基本公共負載均衡器遷移過來的。 可以從 Azure 門戶驗證此情況。

請務必通過標準負載均衡器發送少量流量作為手動測試。
  
以下是有關如何將 VM 添加到新創建的標準公共負載均衡器的後端池的一些方案,以及我們對每個方案的建議:

* **將現有 VM 從舊基本公共負載均衡器的後端池移至新建立的標準公共負載均衡器的後端池**。
    1. 若要進行本快速入門中的工作，請登入 [Azure 入口網站](https://portal.azure.com)。
 
    1. 選擇左方選單上**的資源**,然後從資源清單中選擇**新建立的標準負載均衡器**。
   
    1. 在 [設定]**** 底下選取 [後端集區]****。
   
    1. 選擇與基本負載均衡器的後端池匹配的後端池,選擇以下值: 
      - **虛擬機**:從基本負載均衡器的匹配後端池下拉並選擇 VM。
    1. 選取 [儲存]  。
    >[!NOTE]
    >對於具有公共 IP 的 VM,您需要首先創建標準 IP 位址,而該位址不保證相同的 IP 位址。 取消 VM 與基本 IP 的關聯,並將其與新創建的標準 IP 位址相關聯。 然後,您將能夠按照說明將 VM 添加到標準負載均衡器的後端池中。 

* **建立新 VM 以新增到新建立的標準公共負載均衡器的後端池**。
    * 有關如何創建 VM 並將其與標準負載均衡器關聯的更多說明[,請參閱此處](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)。

### <a name="create-an-outbound-rule-for-outbound-connection"></a>為出站連線建立出站規則

以[說明](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration)建立站規則,以便您可以
* 從頭開始定義出站 NAT。
* 縮放和調整現有出站 NAT 的行為。

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 文本是否有任何限制,可以將配置從 v1 遷移到 v2?

是。 請參考[警告 / 限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 腳本是否還會將流量從基本負載均衡器切換到新創建的標準負載均衡器?

否。 Azure PowerShell 腳本僅遷移配置。 實際流量遷移是您的責任,也是您的控制。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我遇到了使用此腳本的一些問題。 如何獲得説明?
  
您可以向slbupgradesupport@microsoft.com、打開 Azure 支援案例的電子郵件,也可以同時執行此操作。

## <a name="next-steps"></a>後續步驟

[瞭解標準負載均衡器](load-balancer-overview.md)
