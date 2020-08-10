---
title: 從基本內部升級至標準內部 Azure Load Balancer
description: 本文說明如何將 Azure 內部 Load Balancer 從基本 SKU 升級至標準 SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 10a0c186bb1a0d9f974d485e7d2d1d7326bd849c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033150"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>升級 Azure 內部 Load Balancer-不需要輸出連線
[Azure Standard Load Balancer](load-balancer-overview.md)透過區域冗余提供了一組豐富的功能和高可用性。 若要深入瞭解 Load Balancer SKU，請參閱[比較表](https://docs.microsoft.com/azure/load-balancer/skus#skus)。

本文介紹 PowerShell 腳本，它會使用與基本 Load Balancer 相同的設定來建立 Standard Load Balancer，並將來自基本 Load Balancer 的流量遷移到 Standard Load Balancer。

## <a name="upgrade-overview"></a>升級概觀

有 Azure PowerShell 腳本可執行下列動作：

* 在您指定的位置中建立標準內部 SKU Load Balancer。 請注意，標準內部 Load Balancer 不會提供[輸出連接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。
* 將基本 SKU Load Balancer 的設定順暢地複製到新建立的 Standard Load Balancer。
* 將私人 Ip 從基本 Load Balancer 順暢地移至新建立的 Standard Load Balancer。
* 將 Vm 從基本 Load Balancer 的後端集區順暢地移至 Standard Load Balancer 的後端集區

### <a name="caveatslimitations"></a>Caveats\Limitations

* 腳本只支援內部 Load Balancer 升級，而不需要任何輸出連接。 如果您的部分 Vm 需要[輸出](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)連線，請參閱此[頁面](upgrade-InternalBasic-To-PublicStandard.md)以取得相關指示。 
* 基本 Load Balancer 必須位於與後端 Vm 和 Nic 相同的資源群組中。
* 如果在不同的區域中建立標準負載平衡器，您將無法將舊區域中現有的 Vm 與新建立的 Standard Load Balancer 建立關聯。 若要解決這項限制，請務必在新的區域中建立新的 VM。
* 如果您的 Load Balancer 沒有任何前端 IP 設定或後端集區，您可能會遇到執行腳本的錯誤。 請確定它們不是空的。

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>將 [IP 配置方法] 變更為 [前端 IP 設定的靜態] (忽略此步驟（如果它已經是靜態的）) 

1. 選取左側功能表中的 [**所有服務**]，選取 [**所有資源**]，然後從 [資源] 清單中選取您的基本 Load Balancer。

2. 在 [**設定**] 底下，選取 [**前端 ip**設定]，然後選取第一個前端 ip 設定。 

3. 針對 [**指派**]，選取 [**靜態**]

4. 針對基本 Load Balancer 的所有前端 IP 設定重複步驟3。


## <a name="download-the-script"></a>下載腳本

從[PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureILBUpgrade/4.0)下載遷移腳本。
## <a name="use-the-script"></a>使用腳本

有兩個選項可供您選擇，視您的本機 PowerShell 環境設定和偏好而定：

* 如果您未安裝 Azure Az 模組，或不想卸載 Azure Az 模組，最好的方法是使用 `Install-Script` 選項來執行腳本。
* 如果您需要保留 Azure Az 模組，最好是下載並直接執行腳本。

若要判斷您是否已安裝 Azure Az 模組，請執行 `Get-InstalledModule -Name az` 。 如果您看不到任何已安裝的 Az 模組，則可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用安裝腳本方法進行安裝

若要使用此選項，您的電腦上不得安裝 Azure Az 模組。 如果已安裝，下列命令就會顯示錯誤。 您可以卸載 Azure Az 模組，或使用另一個選項手動下載腳本並加以執行。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzureILBUpgrade`

此命令也會安裝必要的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用腳本安裝

如果您已安裝一些 Azure Az 模組，但無法將它們卸載 (或不想要將它們卸載) ，您可以使用腳本下載連結中的 [**手動下載**] 索引標籤，手動下載腳本。 腳本會下載為原始的 nupkg 檔案。 若要從這個 nupkg 檔安裝腳本，請參閱[手動套件下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 使用 `Connect-AzAccount` 來連接到 Azure。

1. 使用匯 `Import-Module Az` 入 Az 模組。

1. 檢查必要的參數：

   * **rgName： [String]： Required** –這是您現有的基本 Load Balancer 和新 Standard Load Balancer 的資源群組。 若要尋找此字串值，請流覽至 Azure 入口網站，選取您的基本 Load Balancer 來源，然後按一下負載平衡器的**總覽**。 資源群組位於該頁面。
   * **oldLBName： [String]： Required** –這是您想要升級的現有基本平衡器名稱。 
   * **newlocation： [String]： Required** –這是將在其中建立 Standard Load Balancer 的位置。 建議將所選基本 Load Balancer 的相同位置繼承到 Standard Load Balancer，以便與其他現有資源更好的關聯。
   * **newLBName： [String]： Required** –這是要建立之 Standard Load Balancer 的名稱。
1. 使用適當的參數來執行腳本。 可能需要五到七分鐘的時間才能完成。

    **範例**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 腳本是否有任何限制，可將設定從 v1 遷移至 v2？

是。 請參閱[警告/限制](#caveatslimitations)。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 腳本是否也會將來自我的基本 Load Balancer 的流量切換到新建立的 Standard Load Balancer？

是，它會遷移流量。 如果您想要單獨遷移流量，請使用[此腳本](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)，這不會為您移動 vm。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我在使用此腳本時遇到一些問題。 如何取得協助？
  
您可以將電子郵件傳送至 slbupgradesupport@microsoft.com ，並使用 Azure 支援服務開啟支援案例，或同時執行這兩項操作。

## <a name="next-steps"></a>後續步驟

[深入瞭解 Standard Load Balancer](load-balancer-overview.md)
