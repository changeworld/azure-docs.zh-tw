---
title: Azure 檔案同步內部部署防火牆和 Proxy 設定 | Microsoft Docs
description: Azure 檔案同步內部部署網路組態
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7410e30c892eb083f9ed71b1d9ce379ae9a036b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515290"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 檔案同步 Proxy 和防火牆設定
Azure 檔案同步會將您的內部部署伺服器連線到 Azure 檔案服務，以啟用多網站同步處理和雲端層功能。 因此，內部部署伺服器必須連線到網際網路。 IT 系統管理員必須決定進入 Azure 雲端服務之伺服器的最佳路徑。

這篇文章會提供特定需求的深入解析，以及可以將伺服器成功且安全地連線到 Azure 檔案同步的選項。

閱讀本操作說明指南之前，建議您先閱讀 [Azure 檔案同步網路功能考量](storage-sync-files-networking-overview.md)。

## <a name="overview"></a>總覽
Azure 檔案同步會作為 Windows Server、Azure 檔案共用和其他數個 Azure 服務之間的協調流程服務，以便如您的同步群組中所述同步處理資料。 為了讓 Azure 檔案同步正常運作，您必須將伺服器設定為與下列 Azure 服務通訊：

- Azure 儲存體
- Azure 檔案同步
- Azure Resource Manager
- 驗證服務

> [!Note]  
> Windows Server 上的 Azure 檔案同步代理程式會啟動對雲端服務的所有要求，因而只需要從防火牆的觀點考慮輸出流量。 <br /> 沒有任何 Azure 服務會啟動與 Azure 檔案同步代理程式的連線。

## <a name="ports"></a>連接埠
Azure 檔案同步會透過 HTTPS 以獨佔方式移動檔案資料和中繼資料，而且要求連接埠 443 開放連出。
如此一來，所有流量都會加密。

## <a name="networks-and-special-connections-to-azure"></a>網路以及 Azure 的特殊連線
Azure 檔案同步代理程式沒有與 Azure 特殊通道 (例如 [ExpressRoute](../../expressroute/expressroute-introduction.md) 等) 相關的需求。

Azure 檔案同步會運用任何可用的方法來允許連線到 Azure，自動調整各種網路特性 (例如頻寬、延遲) ，以及提供系統管理控制權以進行微調。 目前不會提供所有功能。 如果您想要設定特定行為，請透過 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) 讓我們知道。

## <a name="proxy"></a>Proxy
Azure 檔案同步支援應用程式特定和整部電腦的 Proxy 設定。

**應用程式特定的 proxy 設定**可讓您特別針對 Azure 檔案同步流量設定 proxy。 代理程式 4.0.1.0 版或更新版本支援應用程式特定的 Proxy 設定，而且可在代理程式安裝期間進行設定，或使用 Set-StorageSyncProxyConfiguration PowerShell Cmdlet 來設定。

以下 PowerShell 命令可用來設定應用程式特定的 Proxy 設定：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
整個**電腦的 proxy 設定**對於 Azure 檔案同步代理程式而言是透明的，因為伺服器的整個流量都會透過 proxy 路由傳送。

若要設定整部電腦的 Proxy 設定，請遵循下列步驟： 

1. 設定 .NET 應用程式的 Proxy 設定 

   - 編輯這兩個檔案：  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - 在 machine.config 檔案中新增 <system.net> 區段 (在 <system.serviceModel> 區段底下)。  將 127.0.01:8888 變更為 Proxy 伺服器的 IP 位址和連接埠。 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. 設定 WinHTTP Proxy 設定 

   - 從提高權限的命令提示字元或 PowerShell 中執行下列命令，以查看現有的 Proxy 設定：   

     netsh winhttp show proxy

   - 從提高權限的命令提示字元或 PowerShell 中執行下列命令 (將 127.0.01:8888 變更為 Proxy 伺服器的 IP 位址和連接埠)：  

     netsh winhttp set proxy 127.0.0.1:8888

3. 從提高權限的命令提示字元或 PowerShell 中執行下列命令，以重新啟動儲存體同步代理程式服務： 

      net stop filesyncsvc

      注意：儲存體同步代理程式 (filesyncsvc) 服務會在停止後自動啟動。

## <a name="firewall"></a>防火牆
如前一節所述，連接埠 443 必須開放連出。 根據您資料中心、分公司或區域的原則，可能會想要或需要進一步限制透過此連接埠送至特定網域的流量。

下表描述通訊所需的網域：

| 服務 | 公用雲端端點 | Azure Government 端點 | 使用方式 |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | 任何使用者呼叫 (例如 PowerShell) 都會通過這個 URL，包括初始伺服器註冊呼叫。 |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager 呼叫必須由已驗證的使用者進行。 為了獲致成功，此 URL 用於進行使用者驗證。 |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | 在部署 Azure 檔案同步時，將會在訂用帳戶的 Azure Active Directory 中建立服務主體。 針對該目的使用此 URL。 此主體用於將最小權限集合委派給 Azure 檔案同步服務。 執行 Azure 檔案同步之初始設定的使用者，必須是具有訂用帳戶擁有者權限的已驗證使用者。 |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | 使用公用端點 URL。 | 此 URL 是由 Azure 檔案同步伺服器註冊 UI 用來登入系統管理員的 Active Directory 驗證程式庫所存取。 |
| **Azure 儲存體** | &ast;.core.windows.net | &ast;. core.usgovcloudapi.net | 當伺服器下載檔案時，如果伺服器可以直接與儲存體帳戶中的 Azure 檔案共用通話，就可以更有效率地執行資料移動。 伺服器具有只允許存取目標檔案共用的 SAS 金鑰。 |
| **Azure 檔案同步** | &ast;.one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | 初始伺服器註冊之後，伺服器會收到該區域中 Azure 檔案同步服務執行個體的區域 URL。 伺服器可以使用 URL 直接且有效率地與處理其同步的執行個體進行通訊。 |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | 一旦安裝了 Azure 檔案同步代理程式，系統就會使用 PKI URL 來下載與 Azure 檔案同步服務和 Azure 檔案共用通訊時所需的中繼憑證。 OCSP URL 是用來檢查憑證的狀態。 |

> [!Important]
> 允許 afs.azure.net 流量時 &ast; ，流量只可供同步處理服務使用。 沒有其他使用此網域的 Microsoft 服務。
> 當允許前往 &ast;.one.microsoft.com 的流量時，伺服器可能會有前往同步服務以外的流量。 子網域底下有許多 Microsoft 服務可用。

如果 &ast; . afs.azure.net 或 &ast; . one.microsoft.com 太廣泛，您可以只允許與 Azure 檔案儲存體同步服務的明確區域實例通訊，藉此限制伺服器的通訊。 要選擇哪個執行個體，取決於您將伺服器部署及註冊到哪個儲存體同步服務區域。 在下表中，該區域稱為「主要端點 URL」。

基於商務持續性和災害復原 (BCDR) 的理由，您可能已在異地備援 (GRS) 儲存體帳戶中指定 Azure 檔案共用。 如果情況確實如此，則在發生持久的區域中斷時，Azure 檔案共用會容錯移轉至配對的區域。 Azure 檔案同步會使用相同的區域配對作為儲存體。 因此，如果您使用 GRS 儲存體帳戶，您必須啟用其他 Url，讓您的伺服器可以與配對的區域交談，以進行 Azure 檔案同步。下表會呼叫這個「配對區域」。 此外，也必須啟用流量管理員設定檔 URL。 這可確保在發生容錯移轉時，網路流量會順暢地重新路由傳送至配對的區域，這個行為在下表中稱為「探索 URL」。

| Cloud  | 區域 | 主要端點 URL | 配對的區域 | 探索 URL |
|--------|--------|----------------------|---------------|---------------|
| 公用 |澳大利亞東部 | HTTPs： \/ /australiaeast01.afs.azure.net<br>HTTPs： \/ /kailani-aue.one.microsoft.com | 澳大利亞東南部 | HTTPs： \/ /tm-australiaeast01.afs.azure.net<br>HTTPs： \/ /tm-kailani-aue.one.microsoft.com |
| 公用 |澳大利亞東南部 | HTTPs： \/ /australiasoutheast01.afs.azure.net<br>HTTPs： \/ /kailani-aus.one.microsoft.com | 澳大利亞東部 | HTTPs： \/ /tm-australiasoutheast01.afs.azure.net<br>HTTPs： \/ /tm-kailani-aus.one.microsoft.com |
| 公用 | 巴西南部 | HTTPs： \/ /brazilsouth01.afs.azure.net | 美國中南部 | HTTPs： \/ /tm-brazilsouth01.afs.azure.net |
| 公用 | 加拿大中部 | HTTPs： \/ /canadacentral01.afs.azure.net<br>HTTPs： \/ /kailani-cac.one.microsoft.com | 加拿大東部 | HTTPs： \/ /tm-canadacentral01.afs.azure.net<br>HTTPs： \/ /tm-kailani-cac.one.microsoft.com |
| 公用 | 加拿大東部 | HTTPs： \/ /canadaeast01.afs.azure.net<br>HTTPs： \/ /kailani-cae.one.microsoft.com | 加拿大中部 | HTTPs： \/ /tm-canadaeast01.afs.azure.net<br>HTTPs： \/ /tm-kailani.cae.one.microsoft.com |
| 公用 | 印度中部 | HTTPs： \/ /centralindia01.afs.azure.net<br>HTTPs： \/ /kailani-cin.one.microsoft.com | 印度南部 | HTTPs： \/ /tm-centralindia01.afs.azure.net<br>HTTPs： \/ /tm-kailani-cin.one.microsoft.com |
| 公用 | 美國中部 | HTTPs： \/ /centralus01.afs.azure.net<br>HTTPs： \/ /kailani-cus.one.microsoft.com | 美國東部 2 | HTTPs： \/ /tm-centralus01.afs.azure.net<br>HTTPs： \/ /tm-kailani-cus.one.microsoft.com |
| 公用 | 東亞 | HTTPs： \/ /eastasia01.afs.azure.net<br>HTTPs： \/ /kailani11.one.microsoft.com | 東南亞 | HTTPs： \/ /tm-eastasia01.afs.azure.net<br>HTTPs： \/ /tm-kailani11.one.microsoft.com |
| 公用 | 美國東部 | HTTPs： \/ /eastus01.afs.azure.net<br>HTTPs： \/ /kailani1.one.microsoft.com | 美國西部 | HTTPs： \/ /tm-eastus01.afs.azure.net<br>HTTPs： \/ /tm-kailani1.one.microsoft.com |
| 公用 | 美國東部 2 | HTTPs： \/ /eastus201.afs.azure.net<br>HTTPs： \/ /kailani-ess.one.microsoft.com | 美國中部 | HTTPs： \/ /tm-eastus201.afs.azure.net<br>HTTPs： \/ /tm-kailani-ess.one.microsoft.com |
| 公用 | 日本東部 | HTTPs： \/ /japaneast01.afs.azure.net | 日本西部 | HTTPs： \/ /tm-japaneast01.afs.azure.net |
| 公用 | 日本西部 | HTTPs： \/ /japanwest01.afs.azure.net | 日本東部 | HTTPs： \/ /tm-japanwest01.afs.azure.net |
| 公用 | 南韓中部 | HTTPs： \/ /koreacentral01.afs.azure.net/ | 南韓南部 | HTTPs： \/ /tm-koreacentral01.afs.azure.net/ |
| 公用 | 南韓南部 | HTTPs： \/ /koreasouth01.afs.azure.net/ | 南韓中部 | HTTPs： \/ /tm-koreasouth01.afs.azure.net/ |
| 公用 | 美國中北部 | HTTPs： \/ /northcentralus01.afs.azure.net | 美國中南部 | HTTPs： \/ /tm-northcentralus01.afs.azure.net |
| 公用 | 北歐 | HTTPs： \/ /northeurope01.afs.azure.net<br>HTTPs： \/ /kailani7.one.microsoft.com | 西歐 | HTTPs： \/ /tm-northeurope01.afs.azure.net<br>HTTPs： \/ /tm-kailani7.one.microsoft.com |
| 公用 | 美國中南部 | HTTPs： \/ /southcentralus01.afs.azure.net | 美國中北部 | HTTPs： \/ /tm-southcentralus01.afs.azure.net |
| 公用 | 印度南部 | HTTPs： \/ /southindia01.afs.azure.net<br>HTTPs： \/ /kailani-sin.one.microsoft.com | 印度中部 | HTTPs： \/ /tm-southindia01.afs.azure.net<br>HTTPs： \/ /tm-kailani-sin.one.microsoft.com |
| 公用 | 東南亞 | HTTPs： \/ /southeastasia01.afs.azure.net<br>HTTPs： \/ /kailani10.one.microsoft.com | 東亞 | HTTPs： \/ /tm-southeastasia01.afs.azure.net<br>HTTPs： \/ /tm-kailani10.one.microsoft.com |
| 公用 | 英國南部 | HTTPs： \/ /uksouth01.afs.azure.net<br>HTTPs： \/ /kailani-uks.one.microsoft.com | 英國西部 | HTTPs： \/ /tm-uksouth01.afs.azure.net<br>HTTPs： \/ /tm-kailani-uks.one.microsoft.com |
| 公用 | 英國西部 | HTTPs： \/ /ukwest01.afs.azure.net<br>HTTPs： \/ /kailani-ukw.one.microsoft.com | 英國南部 | HTTPs： \/ /tm-ukwest01.afs.azure.net<br>HTTPs： \/ /tm-kailani-ukw.one.microsoft.com |
| 公用 | 美國中西部 | HTTPs： \/ /westcentralus01.afs.azure.net | 美國西部 2 | HTTPs： \/ /tm-westcentralus01.afs.azure.net |
| 公用 | 西歐 | HTTPs： \/ /westeurope01.afs.azure.net<br>HTTPs： \/ /kailani6.one.microsoft.com | 北歐 | HTTPs： \/ /tm-westeurope01.afs.azure.net<br>HTTPs： \/ /tm-kailani6.one.microsoft.com |
| 公用 | 美國西部 | HTTPs： \/ /westus01.afs.azure.net<br>HTTPs： \/ /kailani.one.microsoft.com | 美國東部 | HTTPs： \/ /tm-westus01.afs.azure.net<br>HTTPs： \/ /tm-kailani.one.microsoft.com |
| 公用 | 美國西部 2 | HTTPs： \/ /westus201.afs.azure.net | 美國中西部 | HTTPs： \/ /tm-westus201.afs.azure.net |
| 政府 | US Gov 亞利桑那州 | HTTPs： \/ /usgovarizona01.afs.azure.us | US Gov 德克薩斯州 | HTTPs： \/ /tm-usgovarizona01.afs.azure.us |
| 政府 | US Gov 德克薩斯州 | HTTPs： \/ /usgovtexas01.afs.azure.us | US Gov 亞利桑那州 | HTTPs： \/ /tm-usgovtexas01.afs.azure.us |

- 如果您使用本地備援 (LRS) 或區域備援 (ZRS) 儲存體帳戶，您只需要啟用 [主要端點 URL] 底下所列的 URL。

- 如果您使用異地備援 (GRS) 儲存體帳戶，請啟用三個 URL。

**範例：** 您在 `"West US"` 部署儲存體同步服務，並向其註冊伺服器。 在此案例中，要允許伺服器與之通訊的 URL 是：

> - HTTPs： \/ /westus01.afs.azure.net （主要端點：美國西部）
> - HTTPs： \/ /eastus01.afs.azure.net （配對故障的區域：美國東部）
> - HTTPs： \/ /tm-westus01.afs.azure.net （主要區域的探索 URL）

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>允許 Azure 檔案同步 IP 位址的清單
Azure 檔案同步支援使用服務標籤，此[標記](../../virtual-network/service-tags-overview.md)代表指定 Azure 服務的一組 IP 位址首碼。 您可以使用服務標記來建立防火牆規則，以啟用與 Azure 檔案同步服務的通訊。 Azure 檔案同步的服務標記是 `StorageSyncService` 。

如果您在 Azure 中使用 Azure 檔案同步，您可以直接在網路安全性群組中使用服務標籤的名稱，以允許流量。 若要深入了解如何執行此操作，請參閱[網路安全性群組](../../virtual-network/security-overview.md)。

如果您是使用 Azure 檔案同步內部部署，就可利用服務標籤 API 來取得您防火牆允許清單的特定 IP 位址範圍。 有兩種方法可以取得此資訊：

- 所有支援服務標籤之 Azure 服務的目前 IP 位址範圍清單，都會以 JSON 文件的形式，每週在 Microsoft 下載中心發佈。 每個 Azure 雲端都有自己的 JSON 文件，其中包含與該雲端相關的 IP 位址範圍：
    - [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- 服務標籤探索 API (預覽) 可讓您以程式設計方式擷取目前的服務標籤清單。 在預覽中，服務標籤探索 API 傳回的資訊，可能沒有從 Microsoft 下載中心上發佈的 JSON 文件所傳回的資訊那麼新。 您可以根據您的自動化喜好設定來使用 API 介面：
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

因為服務標籤探索 API 不會經常更新為發行至 Microsoft 下載中心的 JSON 檔，所以我們建議使用 JSON 檔來更新內部部署防火牆的允許清單。 以下步驟可以達到此目的：

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

接著，您可以使用中的 IP 位址範圍 `$ipAddressRanges` 來更新您的防火牆。 如需如何更新防火牆的相關資訊，請參閱防火牆/網路應用裝置的網站。

## <a name="test-network-connectivity-to-service-endpoints"></a>測試與服務端點的網路連線能力
一旦向 Azure 檔案同步服務註冊伺服器之後，就可以使用 StorageSyncNetworkConnectivity Cmdlet 和 ServerRegistration.exe 來測試與此伺服器特定之所有端點（Url）的通訊。 此 Cmdlet 有助於疑難排解未完成的通訊，以防止伺服器完全使用 Azure 檔案同步，而且可以用來微調 proxy 和防火牆設定。

若要執行網路連線測試，請安裝 Azure 檔案同步代理程式9.1 版或更新版本，並執行下列 PowerShell 命令：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>摘要和風險限制
本文件中稍早的清單包含 Azure 檔案同步目前與其通訊的 URL。 防火牆必須能夠允許輸出至這些網域的流量。 Microsoft 致力於讓這份清單保持最新狀態。

設定網域限制防火牆規則可以提高安全性。 如果使用這些防火牆組態，則必須記住 URL 會新增並甚至可能隨著時間而改變。 請定期查看此文章。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
- [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
