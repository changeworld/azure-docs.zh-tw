---
title: 從基本公用升級為標準公用-Azure Load Balancer
description: 本文說明如何將 Azure 公用 Load Balancer 從基本 SKU 升級至標準 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: ef018e58f8336220b96eba568c94efc40a0fb0c7
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045388"
---
# <a name="upgrade-azure-public-load-balancer"></a>升級 Azure 公用 Load Balancer
[Azure Standard Load Balancer](load-balancer-overview.md) 透過區域冗余提供一組豐富的功能和高可用性。 若要深入瞭解 Load Balancer SKU，請參閱 [比較表](./skus.md#skus)。

升級階段分為兩個階段：

1. 將 IP 配置方法從動態變更為靜態。
2. 執行 PowerShell 腳本，以完成升級和流量遷移。

## <a name="upgrade-overview"></a>升級概觀

有 Azure PowerShell 腳本可用來執行下列動作：

* 使用您在基本 Standard Load Balancer 的相同資源群組中指定的位置，建立標準 SKU Load Balancer。
* 就地將公用 IP 位址從基本 SKU 升級到標準 SKU。
* 將基本 SKU Load Balancer 的設定順暢地複製到新建立的 Standard Load Balancer。
* 建立預設輸出規則，以啟用輸出連線能力。

### <a name="caveatslimitations"></a>Caveats\Limitations

* 腳本只支援公用 Load Balancer 升級。 如需瞭解內部基本 Load Balancer 升級，請參閱 [此頁面](./upgrade-basicinternal-standard.md) 以取得相關指示。
* 在執行腳本之前，必須將公用 IP 位址的配置方法變更為「靜態」。 
* 如果您的 Load Balancer 沒有任何前端 IP 設定或後端集區，您可能會遇到執行腳本的錯誤。 請確認它們不是空的。

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>將公用 IP 位址的配置方法變更為靜態

* * * 以下是我們的建議步驟：

    1. 若要進行本快速入門中的工作，請登入 [Azure 入口網站](https://portal.azure.com)。
 
    1. 選取左側功能表上的 [ **所有資源** ]，然後從 [資源] 清單中選取 **與基本 Load Balancer 相關聯的基本公用 IP 位址** 。
   
    1. 在 [設定] 底下 **，選取 [****設定**]。
   
    1. 在 [指派] 下方，選取 [靜態]。
    1. 選取 [儲存]。
    >[!NOTE]
    >對於具有公用 Ip 的 Vm，您必須先建立標準 IP 位址，但不保證相同的 IP 位址。 將 Vm 與基本 ip 解除關聯，並將其與新建立的標準 IP 位址建立關聯。 然後，您將可以依照指示，將 Vm 新增至 Standard Load Balancer 的後端集區。 

* **建立新的 vm，以新增至新建立的標準公用 Load Balancer 的後端** 集區。
    * 您可以在 [這裡](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)找到更多有關如何建立 VM 並將其與 Standard Load Balancer 建立關聯的指示。


## <a name="download-the-script"></a>下載腳本

從  [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0)下載遷移腳本。
## <a name="use-the-script"></a>使用腳本

您可以根據本機 PowerShell 環境的設定和喜好設定，選擇兩個選項：

* 如果您未安裝 Azure Az 模組，或不想要卸載 Azure Az 模組，最佳選項是使用 `Install-Script` 選項來執行腳本。
* 如果您需要保留 Azure Az 模組，最好的做法是直接下載並執行腳本。

若要判斷您是否已安裝 Azure Az 模組，請執行 `Get-InstalledModule -Name az` 。 如果您沒有看到任何已安裝的 Az 模組，則可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用 Install-Script 方法安裝

若要使用此選項，您的電腦上不能安裝 Azure Az 模組。 如果已安裝，則下列命令會顯示錯誤。 您可以卸載 Azure Az 模組，或使用另一個選項來手動下載腳本並加以執行。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzurePublicLBUpgrade`

此命令也會安裝必要的 Az 模組。  

### <a name="install-using-the-script-directly"></a>使用腳本直接安裝

如果您已安裝一些 Azure Az 模組，但無法將它們卸載 (或不想將它們卸載) ，您可以使用腳本下載連結中的 [ **手動下載** ] 索引標籤，手動下載腳本。 腳本會以原始 nupkg 檔案的形式下載。 若要從此 nupkg 檔安裝腳本，請參閱 [手動下載套件](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 使用 `Connect-AzAccount` 連接至 Azure。

1. 用 `Import-Module Az` 來匯入 Az 模組。

1. 檢查必要的參數：

   * **oldRgName： [字串]：必要** 項–這是您現有基本 Load Balancer 您想要升級的資源群組。 若要尋找此字串值，請流覽至 [Azure 入口網站]，選取您的基本 Load Balancer 來源，然後按一下負載平衡器的 **總覽** 。 資源群組位於該頁面上。
   * **oldLBName： [字串]：必要** 項–這是您想要升級的現有基本平衡器名稱。 
   * **newLBName： [字串]： Required** –這是要建立之 Standard Load Balancer 的名稱。
1. 使用適當的參數執行腳本。 可能需要5到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>建立輸出連接的輸出規則

遵循 [指示](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) 來建立輸出規則，讓您可以
* 從頭開始定義輸出 NAT。
* 調整和調整現有輸出 NAT 的行為。

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>將設定從 v1 遷移至 v2 的 Azure PowerShell 腳本是否有任何限制？

可以。 請參閱 [警告/限制](#caveatslimitations)。

### <a name="how-long-does-the-upgrade-take"></a>升級需要多久的時間？

腳本需要大約大約5-10 分鐘的時間才能完成，而且可能需要較長的時間，視 Load Balancer 設定的複雜度而定。 因此，請記住停機時間並規劃容錯移轉（如有必要）。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 腳本是否也會將基本 Load Balancer 的流量切換至新建立的 Standard Load Balancer？

可以。 Azure PowerShell 腳本不只會升級公用 IP 位址、將設定從基本複製到 Standard Load Balancer，也會將 VM 遷移至新建立的標準公用 Load Balancer 後方。 

## <a name="next-steps"></a>下一步

[瞭解 Standard Load Balancer](load-balancer-overview.md)
