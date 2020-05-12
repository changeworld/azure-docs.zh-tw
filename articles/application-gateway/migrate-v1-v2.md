---
title: 從 v1 遷移至 v2-Azure 應用程式閘道
description: 本文說明如何將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 遷移至 v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 57a49f9e1473f33eceba14591815415338aeecf4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198806"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>將 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 遷移至 v2

[Azure 應用程式閘道和 Web 應用程式防火牆（WAF） v2](application-gateway-autoscaling-zone-redundant.md)現已開放下載，可提供自動調整和可用性區域冗余等其他功能。 不過，現有的 v1 閘道不會自動升級為 v2。 如果您想要從 v1 遷移至 v2，請遵循這篇文章中的步驟。

有兩個階段可供遷移：

1. 遷移設定
2. 遷移用戶端流量

本文涵蓋設定遷移。 用戶端流量遷移會依據您的特定環境而有所不同。 不過，[系統會提供](#migrate-client-traffic)一些高階的一般建議。

## <a name="migration-overview"></a>移轉概觀

有 Azure PowerShell 腳本可執行下列動作：

* 在您指定的虛擬網路子網中建立新的 Standard_v2 或 WAF_v2 閘道。
* 將與 v1 標準或 WAF 閘道相關聯的設定順暢地複製到新建立的 Standard_V2 或 WAF_V2 閘道。

### <a name="caveatslimitations"></a>Caveats\Limitations

* 新的 v2 閘道有新的公用和私人 IP 位址。 不可能將與現有 v1 閘道相關聯的 IP 位址順暢地移至 v2。 不過，您可以將現有（未配置）的公用或私人 IP 位址配置給新的 v2 閘道。
* 您必須為您的 v1 閘道所在的虛擬網路中的另一個子網提供 IP 位址空間。 腳本無法在已有 v1 閘道的任何現有子網中建立 v2 閘道。 不過，如果現有的子網已經有 v2 閘道，則在有足夠的 IP 位址空間時，仍然可以使用。
* 若要遷移 TLS/SSL 設定，您必須指定 v1 閘道中使用的所有 TLS/SSL 憑證。
* 如果您已為 V1 閘道啟用 FIPS 模式，則不會將它遷移至新的 v2 閘道。 V2 中不支援 FIPS 模式。
* v2 不支援 IPv6，因此已啟用 IPv6 的 v1 閘道不會遷移。 如果您執行腳本，它可能不會完成。
* 如果 v1 閘道只有私人 IP 位址，腳本會建立新 v2 閘道的公用 IP 位址和私人 IP 位址。 v2 閘道目前不支援私人 IP 位址。
* 名稱包含字母、數位、連字號和底線以外任何專案的標頭不會傳遞至您的應用程式。 這只適用于標頭名稱，而不是標頭值。 這是來自 v1 的重大變更。

## <a name="download-the-script"></a>下載腳本

從[PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAppGWMigration)下載遷移腳本。

## <a name="use-the-script"></a>使用腳本

有兩個選項可供您選擇，視您的本機 PowerShell 環境設定和偏好而定：

* 如果您未安裝 Azure Az 模組，或不想卸載 Azure Az 模組，最好的方法是使用 `Install-Script` 選項來執行腳本。
* 如果您需要保留 Azure Az 模組，最好是下載並直接執行腳本。

若要判斷您是否已安裝 Azure Az 模組，請執行 `Get-InstalledModule -Name az` 。 如果您看不到任何已安裝的 Az 模組，則可以使用 `Install-Script` 方法。

### <a name="install-using-the-install-script-method"></a>使用安裝腳本方法進行安裝

若要使用此選項，您的電腦上不得安裝 Azure Az 模組。 如果已安裝，下列命令就會顯示錯誤。 您可以卸載 Azure Az 模組，或使用另一個選項手動下載腳本並加以執行。
  
使用下列命令來執行腳本，以取得最新版本：

`Install-Script -Name AzureAppGWMigration -Force`

此命令也會安裝必要的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用腳本安裝

如果您已安裝一些 Azure Az 模組，但無法將它們卸載（或不想要將它們卸載），您可以使用腳本下載連結中的 [**手動下載**] 索引標籤，手動下載腳本。 腳本會下載為原始的 nupkg 檔案。 若要從這個 nupkg 檔安裝腳本，請參閱[手動套件下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 使用 `Connect-AzAccount` 來連接到 Azure。

1. 使用匯 `Import-Module Az` 入 Az 模組。

1. 執行 `Get-Help AzureAppGWMigration.ps1` 以檢查必要的參數：

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
   * **resourceId： [String]：必要**-這是您現有的 Standard V1 或 WAF v1 閘道的 AZURE 資源識別碼。 若要尋找此字串值，請流覽至 [Azure 入口網站]，選取您的應用程式閘道或 WAF 資源，然後按一下閘道的 [**屬性**] 連結。 資源識別碼位於該頁面。

     您也可以執行下列 Azure PowerShell 命令來取得資源識別碼：

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange： [String]：必要**-這是您已為新的子網（或您想要配置）指派的 IP 位址空間，其中包含新的 v2 閘道。 這必須以 CIDR 標記法指定。 例如： 10.0.0.0/24。 您不需要事先建立此子網。 如果不存在，腳本會為您建立。
   * **appgwName： [String]：選擇性**。 這是您指定用來做為新 Standard_v2 或 WAF_v2 閘道之名稱的字串。 如果未提供此參數，則會使用現有 v1 閘道的名稱，並將尾碼附加 *_v2* 。
   * **sslCertificates： [PSApplicationGatewaySslCertificate]：選擇性**。  您建立來代表 v1 閘道上的 TLS/SSL 憑證之 PSApplicationGatewaySslCertificate 物件的逗號分隔清單，必須上傳至新的 v2 閘道。 針對標準 v1 或 WAF v1 閘道所設定的每個 TLS/SSL 憑證，您可以透過此處顯示的命令來建立新的 PSApplicationGatewaySslCertificate 物件 `New-AzApplicationGatewaySslCertificate` 。 您需要 TLS/SSL 憑證檔案的路徑和密碼。

     只有在您未針對 v1 閘道或 WAF 設定 HTTPS 接聽程式時，這個參數才是選擇性的。 如果您至少有一個 HTTPS 接聽程式設定，就必須指定此參數。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     您可以在 `$mySslCert1, $mySslCert2` 上述範例中傳入（以逗號分隔），做為腳本中此參數的值。
   * **trustedRootCertificates： [PSApplicationGatewayTrustedRootCertificate]：選擇性**。 以逗號分隔的 PSApplicationGatewayTrustedRootCertificate 物件清單，可讓您建立來代表[受信任的根憑證](ssl-overview.md)，以從您的 v2 閘道驗證後端實例。
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      若要建立 PSApplicationGatewayTrustedRootCertificate 物件的清單，請參閱[AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)。
   * **privateIpAddress： [String]：選擇性**。 您想要與新的 v2 閘道產生關聯的特定私人 IP 位址。  這必須來自您為新的 v2 閘道配置的相同 VNet。 如果未指定，腳本會為您的 v2 閘道配置私人 IP 位址。
   * **publicIpResourceId： [String]：選擇性**。 您的訂用帳戶中，您想要配置給新 v2 閘道的現有公用 IP 位址（標準 SKU）資源的 resourceId。 如果未指定此項，腳本會在相同的資源群組中配置新的公用 IP。 名稱是已附加 *-IP*的 v2 閘道名稱。
   * **validateMigration： [switch]：選擇性**。 如果您想要讓腳本在建立 v2 閘道和設定複本之後執行一些基本設定比較驗證，請使用此參數。 根據預設，不會進行任何驗證。
   * **enableAutoScale： [switch]：選擇性**。 如果您想要腳本在建立新的 v2 閘道上啟用自動調整，請使用此參數。 預設會停用自動調整。 您一律可以在稍後於新建立的 v2 閘道上手動加以啟用。

1. 使用適當的參數來執行腳本。 可能需要五到七分鐘的時間才能完成。

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

首先，再次檢查腳本是否已成功建立新的 v2 閘道，並已從 v1 閘道遷移正確的設定。 您可以從 [Azure 入口網站] 進行驗證。

此外，透過 v2 閘道傳送少量的流量作為手動測試。
  
以下是您目前的應用程式閘道（標準）可能會接收到用戶端流量的幾個案例，以及我們針對每一個的建議：

* **自訂 DNS 區域（例如 contoso.com），指向與您的標準 v1 或 WAF v1 閘道相關聯的前端 IP 位址（使用 A 記錄）**。

    您可以更新您的 DNS 記錄，以指向與 Standard_v2 應用程式閘道相關聯的前端 IP 或 DNS 標籤。 視 DNS 記錄上設定的 TTL 而定，您的所有用戶端流量可能需要一些時間才能遷移至新的 v2 閘道。
* **自訂 dns 區域（例如，contoso.com），其指向與您 v1 閘道相關聯的 DNS 標籤（例如：使用 CNAME 記錄*myappgw.eastus.cloudapp.azure.com* ）**。

   您有兩個選擇：

  * 如果您在應用程式閘道上使用公用 IP 位址，您可以使用流量管理員設定檔進行受控制的細微遷移，以累加方式將流量（加權流量路由方法）路由傳送至新的 v2 閘道。

    若要這麼做，您可以將 v1 和 v2 應用程式閘道的 DNS 標籤新增至[流量管理員設定檔](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)，並將自訂 DNS 記錄（例如， `www.contoso.com` ） CNAMEing 至流量管理員網域（例如 contoso.trafficmanager.net）。
  * 或者，您可以更新您的自訂網域 DNS 記錄，以指向新 v2 應用程式閘道的 DNS 標籤。 視 DNS 記錄上設定的 TTL 而定，您的所有用戶端流量可能需要一些時間才能遷移至新的 v2 閘道。
* **您的用戶端會連線至您應用程式閘道的前端 IP 位址**。

   更新您的用戶端，以使用與新建立的 v2 應用程式閘道相關聯的 IP 位址。 我們建議您不要直接使用 IP 位址。 請考慮使用與您的應用程式閘道相關聯的 DNS 名稱標籤（例如，yourgateway.eastus.cloudapp.azure.com），您可以將其 CNAME 至您自己的自訂 DNS 區域（例如 contoso.com）。

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 腳本是否有任何限制，可將設定從 v1 遷移至 v2？

是。 請參閱[警告/限制](#caveatslimitations)。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>這篇文章和 Azure PowerShell 腳本也適用于應用程式閘道 WAF 產品嗎？ 

是。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell 腳本是否也會將來自 v1 閘道的流量切換到新建立的 v2 閘道？

否。 Azure PowerShell 腳本只會遷移設定。 實際的流量遷移是您在控制中的責任。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 腳本所建立的新 v2 閘道是否會適當地調整大小，以處理我的 v1 閘道目前所提供的所有流量？

Azure PowerShell 腳本會建立具有適當大小的新 v2 閘道，以處理您現有 v1 閘道上的流量。 自動調整功能預設為停用，但您可以在執行腳本時啟用自動調整。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>我設定了 v1 閘道，以將記錄傳送至 Azure 儲存體。 腳本是否也會針對 v2 複寫此設定？

否。 此腳本不會複寫 v2 的此設定。 您必須將記錄檔設定分別新增至已遷移的 v2 閘道。

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>此腳本是否支援將憑證上傳至 Azure KeyVault？

否。 目前腳本不支援 KeyVault 中的憑證。 不過，未來的版本將會考慮這一點。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我在使用此腳本時遇到一些問題。 如何取得協助？
  
您可以在「設定和安裝/遷移至 V2 SKU」主題中，聯絡 Azure 支援。 [在這裡](https://azure.microsoft.com/support/options/)深入瞭解 Azure 支援。

## <a name="next-steps"></a>後續步驟

[深入瞭解應用程式閘道 v2](application-gateway-autoscaling-zone-redundant.md)
