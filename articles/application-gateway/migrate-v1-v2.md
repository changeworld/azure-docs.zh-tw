---
title: 從 v1 遷移到 v2 - Azure 應用程式閘道
description: 本文介紹如何將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 遷移到 v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9909c46015fffb3bea3eef094599312e28b935c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77046205"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 遷移到 v2

[Azure 應用程式閘道和 Web 應用程式防火牆 （WAF） v2](application-gateway-autoscaling-zone-redundant.md)現已可用，提供其他功能，如自動縮放和可用性區域冗余。 不過，現有的 v1 閘道不會自動升級為 v2。 如果要從 v1 遷移到 v2，請按照本文中的步驟操作。

遷移分為兩個階段：

1. 遷移配置
2. 遷移用戶端流量

本文介紹配置遷移。 用戶端流量遷移因特定環境而異。 然而，[提供了](#migrate-client-traffic)一些高層次的一般性建議。

## <a name="migration-overview"></a>移轉概觀

Azure PowerShell 腳本可用於執行以下操作：

* 在指定的虛擬網路子網中創建新Standard_v2或WAF_v2閘道。
* 將與 v1 標準或 WAF 閘道關聯的配置無縫複製到新創建的Standard_V2或WAF_V2閘道。

### <a name="caveatslimitations"></a>警告\限制

* 新的 v2 閘道具有新的公共和私人 IP 位址。 無法將與現有 v1 閘道關聯的 IP 位址無縫移動到 v2。 但是，您可以將現有（未分配）公共或私人 IP 位址分配給新的 v2 閘道。
* 您必須為 v1 閘道所在的虛擬網路中的另一個子網提供 IP 位址空間。 腳本無法在任何已具有 v1 閘道的現有子網中創建 v2 閘道。 但是，如果現有子網已具有 v2 閘道，則只要有足夠的 IP 位址空間，該閘道可能仍然有效。
* 要遷移 SSL 配置，必須指定 v1 閘道中使用的所有 SSL 憑證。
* 如果 V1 閘道啟用了 FIPS 模式，則不會將其遷移到新的 v2 閘道。 v2 不支援 FIPS 模式。
* v2 不支援 IPv6，因此不會遷移啟用 IPv6 的 v1 閘道。 如果運行腳本，它可能未完成。
* 如果 v1 閘道只有私人 IP 位址，則腳本會為新 v2 閘道創建公共 IP 位址和私人 IP 位址。 v2 閘道當前不支援私人 IP 位址。

## <a name="download-the-script"></a>下載腳本

從[PowerShell 庫](https://www.powershellgallery.com/packages/AzureAppGWMigration)下載遷移腳本。

## <a name="use-the-script"></a>使用腳本

根據您的本地 PowerShell 環境設置和首選項，有兩個選項可供您使用：

* 如果未安裝 Azure Az 模組，或者不介意卸載 Azure Az 模組，則最佳選項是使用`Install-Script`選項運行腳本。
* 如果需要保留 Azure Az 模組，最好下載腳本並直接運行它。

要確定是否安裝了 Azure Az 模組，請`Get-InstalledModule -Name az`運行 。 如果看不到任何已安裝的 Az 模組，則可以使用 方法`Install-Script`。

### <a name="install-using-the-install-script-method"></a>使用安裝腳本方法進行安裝

要使用此選項，不得在電腦上安裝 Azure Az 模組。 如果安裝了它們，則以下命令將顯示錯誤。 您可以卸載 Azure Az 模組，也可以使用另一個選項手動下載腳本並運行腳本。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzureAppGWMigration`

此命令還安裝所需的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用腳本安裝

如果確實安裝了某些 Azure Az 模組，但無法卸載它們（或不想卸載它們），則可以使用腳本下載連結中的 **"手動下載**"選項卡手動下載腳本。 腳本下載為原始 nupkg 檔。 要從此 nupkg 檔安裝腳本，請參閱[手動包下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 用於`Connect-AzAccount`連接到 Azure。

1. 用於`Import-Module Az`導入 Az 模組。

1. 運行`Get-Help AzureAppGWMigration.ps1`以檢查所需的參數：

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   腳本的參數：
   * **資源 Id： [字串]： 必需**- 這是現有標準 v1 或 WAF v1 閘道的 Azure 資源識別碼。 要查找此字串值，請導航到 Azure 門戶，選擇應用程式閘道或 WAF 資源，然後按一下**閘道的屬性連結**。 資源識別碼 位於該頁上。

     您還可以運行以下 Azure PowerShell 命令來獲取資源識別碼：

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **子網位址範圍： [String]： 必需**- 這是您為包含新 v2 閘道的新子網分配（或想要分配）的 IP 位址空間。 必須在 CIDR 符號中指定這一點。 例如：10.0.0.0/24。 您無需提前創建此子網。 如果腳本不存在，則為其創建它。
   * **應用名稱： [字串]： 可選**。 這是指定用作新Standard_v2或WAF_v2閘道的名稱的字串。 如果未提供此參數，則現有 v1 閘道的名稱將與附加的尾碼 *_v2*一起使用。
   * **ssl 證書： [PS應用程式閘道Ssl證書]： 可選**。  您必須將創建以表示來自 v1 閘道的 SSL 憑證的 PSApplicationGatewaySsl證書物件的逗號分隔清單上載到新的 v2 閘道。 對於為標準 v1 或 WAF v1 閘道配置的每個 SSL 憑證，您可以通過此處顯示的命令創建新的`New-AzApplicationGatewaySslCertificate`PSApplicationGatewaySsl證書物件。 您需要 SSL 憑證檔和密碼的路徑。

     如果您沒有為 v1 閘道或 WAF 配置 HTTPS 攔截器，則此參數僅可選。 如果至少有一個 HTTPS 攔截器設置，則必須指定此參數。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     您可以在前面的示例中作為`$mySslCert1, $mySslCert2`腳本中此參數的值傳遞（逗號分隔）。
   * **受信任的根憑證： [PS應用程式閘道受信任的 Root 證書]： 可選**。 您創建的 PSApplicationGatewayTrustedRoot證書物件的逗號分隔清單，用於表示用於從 v2 閘道對後端實例進行身份驗證的[可信根憑證](ssl-overview.md)。
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      要創建 PSApplicationGatewayTrustedRoot證書物件的清單，請參閱[新應用程式閘道受信任的 Root 證書](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)。
   * **私有 Ip 位址： [字串]： 可選**。 要關聯到新 v2 閘道的特定私人 IP 位址。  這必須來自您為新 v2 閘道分配的同一個 VNet。 如果未指定，腳本將為您的 v2 閘道分配私人 IP 位址。
   * **公共Ip資源Id： [字串]： 可選**。 要分配給新 v2 閘道的訂閱中現有公共 IP 位址 （標準 SKU） 資源的資源識別碼。 如果未指定，腳本將在同一資源組中分配新的公共 IP。 名稱是 v2 閘道的名稱，附加了 *-IP。*
   * **驗證遷移： [開關]： 可選**。 如果希望腳本在 v2 閘道創建和配置副本後執行一些基本配置比較驗證，請使用此參數。 預設情況下，不執行任何驗證。
   * **啟用自動縮放：[開關]：可選**。 如果希望腳本在創建新 v2 閘道後啟用自動縮放，請使用此參數。 預設情況下，自動縮放是禁用的。 您始終可以在以後新創建的 v2 閘道上手動啟用它。

1. 使用適當的參數運行腳本。 可能需要五到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>遷移用戶端流量

首先，仔細檢查腳本成功創建了新的 v2 閘道，其確切配置是從 v1 閘道遷移過來的。 可以從 Azure 門戶驗證此情況。

此外，通過 v2 閘道發送少量流量作為手動測試。
  
下面是以下幾個方案，您當前的應用程式閘道 （Standard） 可能會接收用戶端流量，以及我們對每個請求的建議：

* **指向與標準 v1 或 WAF v1 閘道關聯的前端 IP 位址（使用 A 記錄）的自訂 DNS 區域（例如contoso.com。**

    您可以更新 DNS 記錄以指向與Standard_v2應用程式閘道關聯的前端 IP 或 DNS 標籤。 根據 DNS 記錄上配置的 TTL，所有用戶端流量可能需要一段時間才能遷移到新的 v2 閘道。
* **指向與 v1 閘道關聯的 DNS 標籤（例如 *：myappgw.eastus.cloudapp.azure.com*使用 CNAME 記錄）的自訂 DNS 區域（例如contoso.com）。**

   您有兩個選擇：

  * 如果在應用程式閘道上使用公共 IP 位址，則可以使用流量管理器設定檔執行受控的精細遷移，以增量方式將流量（加權流量路由方法）路由到新的 v2 閘道。

    為此，可以將 v1 和 v2 應用程式閘道的 DNS 標籤添加到[流量管理器設定檔](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)，並將自訂 DNS 記錄（例如`www.contoso.com`）CNAME 添加到流量管理器域（例如，contoso.trafficmanager.net）。
  * 或者，您可以更新自訂域 DNS 記錄以指向新 v2 應用程式閘道的 DNS 標籤。 根據 DNS 記錄上配置的 TTL，所有用戶端流量可能需要一段時間才能遷移到新的 v2 閘道。
* **您的用戶端連接到應用程式閘道的前端 IP 位址**。

   更新用戶端以使用與新創建的 v2 應用程式閘道關聯的 IP 位址。 我們建議您不要直接使用 IP 位址。 請考慮使用與應用程式閘道關聯的 DNS 名稱標籤（例如yourgateway.eastus.cloudapp.azure.com），您可以將該標籤 CNAME 連接到自己的自訂 DNS 區域（例如，contoso.com）。

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 腳本是否有任何限制，可以將配置從 v1 遷移到 v2？

是。 請參閱[警告/限制](#caveatslimitations)。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>本文和 Azure PowerShell 腳本是否也適用于應用程式閘道 WAF 產品？ 

是。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 腳本是否還會將流量從 v1 閘道切換到新創建的 v2 閘道？

否。 Azure PowerShell 腳本僅遷移配置。 實際流量遷移是您的責任，也是您的控制。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 腳本創建的新 v2 閘道的大小是否適當，以處理 v1 閘道當前提供的所有流量？

Azure PowerShell 腳本創建具有適當大小的新 v2 閘道，以處理現有 v1 閘道上的流量。 預設情況下禁用自動縮放，但在運行腳本時可以啟用"自動縮放"。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>我配置了 v1 閘道，以便將日誌發送到 Azure 存儲。 腳本是否也會複製 v2 的此配置？

否。 腳本不復制 v2 的此配置。 您必須將日誌配置單獨添加到遷移的 v2 閘道。

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>此腳本是否支援上載到 Azure 金鑰庫的證書？

否。 目前，腳本不支援 KeyVault 中的證書。 但是，正在考慮未來版本。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我遇到了使用此腳本的一些問題。 如何獲得説明？
  
您可以向appgwmigrationsup@microsoft.com、打開 Azure 支援案例的電子郵件，也可以同時執行此操作。

## <a name="next-steps"></a>後續步驟

[瞭解應用程式閘道 v2](application-gateway-autoscaling-zone-redundant.md)
