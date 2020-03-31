---
title: Azure 檔案同步內部部署防火牆和 Proxy 設定 | Microsoft Docs
description: Azure 檔案同步內部部署網路組態
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294514"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 檔案同步 Proxy 和防火牆設定
Azure 檔案同步會將您的內部部署伺服器連線到 Azure 檔案服務，以啟用多網站同步處理和雲端層功能。 因此，內部部署伺服器必須連線到網際網路。 IT 系統管理員必須決定進入 Azure 雲端服務之伺服器的最佳路徑。

這篇文章會提供特定需求的深入解析，以及可以將伺服器成功且安全地連線到 Azure 檔案同步的選項。

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

**應用程式特定的 Proxy 設定**可特別針對 Azure 檔案同步流量允許 Proxy 的組態。 代理程式 4.0.1.0 版或更新版本支援應用程式特定的 Proxy 設定，而且可在代理程式安裝期間進行設定，或使用 Set-StorageSyncProxyConfiguration PowerShell Cmdlet 來設定。

以下 PowerShell 命令可用來設定應用程式特定的 Proxy 設定：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**整部電腦的 Proxy 設定**對於 Azure 檔案同步代理程式而言是透明的，因為伺服器的整個流量都會透過 Proxy 路由傳送。

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

| 服務 | 公共雲終結點 | Azure Government 端點 | 使用量 |
|---------|----------------|---------------|------------------------------|
| **Azure 資源管理器** | `https://management.azure.com` | https://management.usgovcloudapi.net | 任何使用者呼叫 (例如 PowerShell) 都會通過這個 URL，包括初始伺服器註冊呼叫。 |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager 呼叫必須由已驗證的使用者進行。 為了獲致成功，此 URL 用於進行使用者驗證。 |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | 在部署 Azure 檔案同步時，將會在訂用帳戶的 Azure Active Directory 中建立服務主體。 針對該目的使用此 URL。 此主體用於將最小權限集合委派給 Azure 檔案同步服務。 執行 Azure 檔案同步之初始設定的使用者，必須是具有訂用帳戶擁有者權限的已驗證使用者。 |
| **Azure 存儲** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | 當伺服器下載檔案時，如果伺服器可以直接與儲存體帳戶中的 Azure 檔案共用通話，就可以更有效率地執行資料移動。 伺服器具有只允許存取目標檔案共用的 SAS 金鑰。 |
| **Azure 檔案同步** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | 初始伺服器註冊之後，伺服器會收到該區域中 Azure 檔案同步服務執行個體的區域 URL。 伺服器可以使用 URL 直接且有效率地與處理其同步的執行個體進行通訊。 |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | 一旦安裝了 Azure 檔案同步代理程式，系統就會使用 PKI URL 來下載與 Azure 檔案同步服務和 Azure 檔案共用通訊時所需的中繼憑證。 OCSP URL 是用來檢查憑證的狀態。 |

> [!Important]
> 當允許前往 &ast;.one.microsoft.com 的流量時，伺服器可能會有前往同步服務以外的流量。 子網域底下有許多 Microsoft 服務可用。

如果 &ast;.one.microsoft.com 太廣泛，您可只允許對 Azure 檔案同步服務的明確區域執行個體進行通訊，藉此限制伺服器的通訊。 要選擇哪個執行個體，取決於您將伺服器部署及註冊到哪個儲存體同步服務區域。 在下表中，該區域稱為「主要端點 URL」。

基於商務持續性和災害復原 (BCDR) 的理由，您可能已在異地備援 (GRS) 儲存體帳戶中指定 Azure 檔案共用。 如果情況確實如此，則在發生持久的區域中斷時，Azure 檔案共用會容錯移轉至配對的區域。 Azure 檔案同步會使用相同的區域配對作為儲存體。 因此，如果使用 GRS 存儲帳戶，則需要啟用其他 URL，以允許伺服器與 Azure 檔同步的配對區域對話。下表稱之為"配對區域"。 此外，也必須啟用流量管理員設定檔 URL。 這可確保在發生容錯移轉時，網路流量會順暢地重新路由傳送至配對的區域，這個行為在下表中稱為「探索 URL」。

| Cloud  | 區域 | 主要端點 URL | 配對的區域 | 探索 URL |
|--------|--------|----------------------|---------------|---------------|
| 公開 |澳大利亞東部 | HTTPs：\//kailani-aue.one.microsoft.com | 澳大利亞東南部 | HTTPs：\//tm-kailani-aue.one.microsoft.com |
| 公開 |澳大利亞東南部 | HTTPs：\//kailani-aus.one.microsoft.com | 澳大利亞東部 | HTTPs：\//tm-kailani-aus.one.microsoft.com |
| 公開 | 巴西南部 | HTTPs：\//brazilsouth01.afs.azure.net | 美國中南部 | HTTPs：\//tm-brazilsouth01.afs.azure.net |
| 公開 | 加拿大中部 | HTTPs：\//kailani-cac.one.microsoft.com | 加拿大東部 | HTTPs：\//tm-kailani-cac.one.microsoft.com |
| 公開 | 加拿大東部 | HTTPs：\//kailani-cae.one.microsoft.com | 加拿大中部 | HTTPs：\//tm-kailani.cae.one.microsoft.com |
| 公開 | 印度中部 | HTTPs：\//kailani-cin.one.microsoft.com | 印度南部 | HTTPs：\//tm-kailani-cin.one.microsoft.com |
| 公開 | 美國中部 | HTTPs：\//kailani-cus.one.microsoft.com | 美國東部 2 | HTTPs：\//tm-kailani-cus.one.microsoft.com |
| 公開 | 東亞 | HTTPs：\//kailani11.one.microsoft.com | 東南亞 | HTTPs：\//tm-kailani11.one.microsoft.com |
| 公開 | 美國東部 | HTTPs：\//kailani1.one.microsoft.com | 美國西部 | HTTPs：\//tm-kailani1.one.microsoft.com |
| 公開 | 美國東部 2 | HTTPs：\//kailani-ess.one.microsoft.com | 美國中部 | HTTPs：\//tm-kailani-ess.one.microsoft.com |
| 公開 | 日本東部 | HTTPs：\//japaneast01.afs.azure.net | 日本西部 | HTTPs：\//tm-japaneast01.afs.azure.net |
| 公開 | 日本西部 | HTTPs：\//japanwest01.afs.azure.net | 日本東部 | HTTPs：\//tm-japanwest01.afs.azure.net |
| 公開 | 南韓中部 | HTTPs：\//koreacentral01.afs.azure.net/ | 南韓南部 | HTTPs：\//tm-koreacentral01.afs.azure.net/ |
| 公開 | 南韓南部 | HTTPs：\//koreasouth01.afs.azure.net/ | 南韓中部 | HTTPs：\//tm-koreasouth01.afs.azure.net/ |
| 公開 | 美國中北部 | HTTPs：\//northcentralus01.afs.azure.net | 美國中南部 | HTTPs：\//tm-northcentralus01.afs.azure.net |
| 公開 | 北歐 | HTTPs：\//kailani7.one.microsoft.com | 西歐 | HTTPs：\//tm-kailani7.one.microsoft.com |
| 公開 | 美國中南部 | HTTPs：\//southcentralus01.afs.azure.net | 美國中北部 | HTTPs：\//tm-southcentralus01.afs.azure.net |
| 公開 | 印度南部 | HTTPs：\//kailani-sin.one.microsoft.com | 印度中部 | HTTPs：\//tm-kailani-sin.one.microsoft.com |
| 公開 | 東南亞 | HTTPs：\//kailani10.one.microsoft.com | 東亞 | HTTPs：\//tm-kailani10.one.microsoft.com |
| 公開 | 英國南部 | HTTPs：\//kailani-uks.one.microsoft.com | 英國西部 | HTTPs：\//tm-kailani-uks.one.microsoft.com |
| 公開 | 英國西部 | HTTPs：\//kailani-ukw.one.microsoft.com | 英國南部 | HTTPs：\//tm-kailani-ukw.one.microsoft.com |
| 公開 | 美國中西部 | HTTPs：\//westcentralus01.afs.azure.net | 美國西部 2 | HTTPs：\//tm-westcentralus01.afs.azure.net |
| 公開 | 西歐 | HTTPs：\//kailani6.one.microsoft.com | 北歐 | HTTPs：\//tm-kailani6.one.microsoft.com |
| 公開 | 美國西部 | HTTPs：\//kailani.one.microsoft.com | 美國東部 | HTTPs：\//tm-kailani.one.microsoft.com |
| 公開 | 美國西部 2 | HTTPs：\//westus201.afs.azure.net | 美國中西部 | HTTPs：\//tm-westus201.afs.azure.net |
| 政府 | US Gov 亞利桑那州 | HTTPs：\//usgovarizona01.afs.azure.us | US Gov 德克薩斯州 | HTTPs：\//tm-usgovarizona01.afs.azure.us |
| 政府 | US Gov 德克薩斯州 | HTTPs：\//usgovtexas01.afs.azure.us | US Gov 亞利桑那州 | HTTPs：\//tm-usgovtexas01.afs.azure.us |

- 如果您使用本地備援 (LRS) 或區域備援 (ZRS) 儲存體帳戶，您只需要啟用 [主要端點 URL] 底下所列的 URL。

- 如果您使用異地備援 (GRS) 儲存體帳戶，請啟用三個 URL。

**範例：** 您在 `"West US"` 部署儲存體同步服務，並向其註冊伺服器。 在此案例中，要允許伺服器與之通訊的 URL 是：

> - HTTPs：\//kailani.one.microsoft.com （主終結點： 美國西部）
> - HTTPs：\//kailani1.one.microsoft.com （配對容錯移轉區域： 美國東部）
> - HTTPs：\//tm-kailani.one.microsoft.com（主區域的發現 URL）

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>允許 Azure 檔同步 IP 位址清單
Azure 檔同步支援使用[服務標記](../../virtual-network/service-tags-overview.md)，它表示給定 Azure 服務的一組 IP 位址首碼。 可以使用服務標記創建防火牆規則，以啟用與 Azure 檔同步服務的通信。 Azure 檔同步的服務標記為`StorageSyncService`。

如果在 Azure 中使用 Azure 檔同步，則可以直接在網路安全性群組中使用服務標記的名稱來允許流量。 要瞭解有關如何執行此操作的更多資訊，請參閱[網路安全性群組](../../virtual-network/security-overview.md)。

如果在本地使用 Azure 檔同步，則可以使用服務標記 API 獲取防火牆允許清單的特定 IP 位址範圍。 有兩種方法可以獲取此資訊：

- 支援服務標記的所有 Azure 服務標記的當前 IP 位址範圍清單每週以 JSON 文檔的形式在 Microsoft 下載中心發佈。 每個 Azure 雲都有自己的 JSON 文檔，其 IP 位址範圍與該雲相關：
    - [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure 美國政府](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure 中國](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- 服務標記發現 API（預覽）允許以程式設計方式檢索當前服務標記清單。 在預覽中，服務標記發現 API 可能會返回比從 Microsoft 下載中心上發佈的 JSON 文檔返回的資訊更最新的資訊。 您可以根據您的自動化首選項使用 API 曲面：
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure 電源外殼](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

由於服務標記發現 API 的更新頻率不如發佈到 Microsoft 下載中心的 JSON 文檔，因此建議使用 JSON 文檔更新本地防火牆的允許清單。 以下步驟可以達到此目的：

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

然後，您可以使用 中的`$ipAddressRanges`IP 位址範圍來更新防火牆。 有關如何更新防火牆的資訊，請查看防火牆/網路設備的網站。

## <a name="test-network-connectivity-to-service-endpoints"></a>測試與服務終結點的網路連接
一旦伺服器註冊到 Azure 檔同步服務，測試存儲同步連接 Cmdlet 和伺服器註冊.exe 可用於測試與特定于此伺服器的所有終結點 （URL） 的通信。 當通信不完整阻止伺服器完全使用 Azure 檔同步並且可用於微調代理和防火牆配置時，此 Cmdlet 可説明進行故障排除。

要運行網路連接測試，請安裝 Azure 檔同步代理版本 9.1 或更高版本，並運行以下 PowerShell 命令：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>摘要和風險限制
本文件中稍早的清單包含 Azure 檔案同步目前與其通訊的 URL。 防火牆必須能夠允許輸出至這些網域的流量。 Microsoft 致力於讓這份清單保持最新狀態。

設定網域限制防火牆規則可以提高安全性。 如果使用這些防火牆組態，則必須記住 URL 會新增並甚至可能隨著時間而改變。 請定期查看此文章。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
- [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
