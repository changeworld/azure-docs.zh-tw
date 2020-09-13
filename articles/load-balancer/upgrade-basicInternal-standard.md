---
title: 從基本內部升級為標準內部 Azure Load Balancer
description: 本文說明如何將 Azure Internal Load Balancer 從基本 SKU 升級至標準 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: a6d2b69b0b498601497c4b33fb6bdfede87002df
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500244"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升級 Azure 內部 Load Balancer-不需要輸出連接
[Azure Standard Load Balancer](load-balancer-overview.md) 透過區域冗余提供一組豐富的功能和高可用性。 若要深入瞭解 Load Balancer SKU，請參閱 [比較表](https://docs.microsoft.com/azure/load-balancer/skus#skus)。

本文介紹 PowerShell 腳本，該腳本會使用與基本 Load Balancer 相同的設定來建立 Standard Load Balancer，並將流量從基本 Load Balancer 遷移至 Standard Load Balancer。

## <a name="upgrade-overview"></a>升級概觀

有 Azure PowerShell 腳本可用來執行下列動作：

* 在您指定的位置中建立標準內部 SKU Load Balancer。 請注意，標準的內部 Load Balancer 不會提供任何 [輸出連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 。
* 將基本 SKU Load Balancer 的設定順暢地複製到新建立的 Standard Load Balancer。
* 順暢地將私人 Ip 從基本 Load Balancer 移至新建立的 Standard Load Balancer。
* 將 Vm 從基本 Load Balancer 的後端集區順暢地移至 Standard Load Balancer 的後端集區

### <a name="caveatslimitations"></a>Caveats\Limitations

* 腳本僅支援內部 Load Balancer 升級，但不需要任何輸出連接。 如果您需要部分 Vm 的 [輸出](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 連線，請參閱此 [頁面](upgrade-InternalBasic-To-PublicStandard.md) 以取得相關指示。 
* 基本 Load Balancer 必須與後端 Vm 和 Nic 位於相同的資源群組中。
* 如果在不同區域中建立標準負載平衡器，您將無法將舊區域中現有的 Vm 與新建立的 Standard Load Balancer 建立關聯。 若要解決這項限制，請務必在新區域中建立新的 VM。
* 如果您的 Load Balancer 沒有任何前端 IP 設定或後端集區，您可能會遇到執行腳本的錯誤。 請確定它們不是空的。

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>將前端 IP 設定的 IP 配置方法變更為靜態， (如果已有靜態，請略過此步驟) 

1. 在左側功能表中選取 [ **所有服務** ]，選取 [ **所有資源**]，然後從 [資源] 清單中選取您的基本 Load Balancer。

2. 在 [ **設定**] 底下，選取 [ **前端 ip**設定]，然後選取第一個前端 ip 設定。 

3. 針對 [**指派**]，選取 [**靜態**]

4. 針對基本 Load Balancer 的所有前端 IP 設定重複步驟3。


## <a name="download-the-script"></a>下載腳本

從  [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0)下載遷移腳本。
## <a name="use-the-script"></a>使用腳本

您可以根據本機 PowerShell 環境的設定和喜好設定，選擇兩個選項：

* 如果您未安裝 Azure Az 模組，或不想要卸載 Azure Az 模組，最佳選項是使用 `Install-Script` 選項來執行腳本。
* 如果您需要保留 Azure Az 模組，最好的做法是直接下載並執行腳本。

若要判斷您是否已安裝 Azure Az 模組，請執行 `Get-InstalledModule -Name az` 。 如果您沒有看到任何已安裝的 Az 模組，則可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用安裝腳本方法安裝

若要使用此選項，您的電腦上不能安裝 Azure Az 模組。 如果已安裝，則下列命令會顯示錯誤。 您可以卸載 Azure Az 模組，或使用另一個選項來手動下載腳本並加以執行。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzureILBUpgrade`

此命令也會安裝必要的 Az 模組。  

### <a name="install-using-the-script-directly"></a>使用腳本直接安裝

如果您已安裝一些 Azure Az 模組，但無法將它們卸載 (或不想將它們卸載) ，您可以使用腳本下載連結中的 [ **手動下載** ] 索引標籤，手動下載腳本。 腳本會以原始 nupkg 檔案的形式下載。 若要從此 nupkg 檔安裝腳本，請參閱 [手動下載套件](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 使用 `Connect-AzAccount` 連接至 Azure。

1. 用 `Import-Module Az` 來匯入 Az 模組。

1. 檢查必要的參數：

   * **rgName： [字串]：必要** 項–這是您現有基本 Load Balancer 和新 Standard Load Balancer 的資源群組。 若要尋找此字串值，請流覽至 [Azure 入口網站]，選取您的基本 Load Balancer 來源，然後按一下負載平衡器的 **總覽** 。 資源群組位於該頁面上。
   * **oldLBName： [字串]：必要** 項–這是您想要升級的現有基本平衡器名稱。 
   * **newlocation： [字串]：必要** 項–這是將建立 Standard Load Balancer 的位置。 建議您將所選基本 Load Balancer 的相同位置繼承到 Standard Load Balancer，以便與其他現有資源更好的關聯。
   * **newLBName： [字串]： Required** –這是要建立之 Standard Load Balancer 的名稱。
1. 使用適當的參數執行腳本。 可能需要5到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>將設定從 v1 遷移至 v2 的 Azure PowerShell 腳本是否有任何限制？

可以。 請參閱 [警告/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 腳本是否也會將基本 Load Balancer 的流量切換至新建立的 Standard Load Balancer？

是的，它會遷移流量。 如果您想要以個人的方式遷移流量，請使用 [此腳本，此腳本](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) 不會為您移動 vm。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我在使用此腳本時遇到一些問題。 如何取得協助？
  
您可以傳送電子郵件給 slbupgradesupport@microsoft.com 、使用 Azure 支援來開啟支援案例，或同時進行這兩項作業。

## <a name="next-steps"></a>接下來的步驟

[瞭解 Standard Load Balancer](load-balancer-overview.md)
