---
title: Azure 檔案同步內部部署防火牆和 Proxy 設定 | Microsoft Docs
description: Azure 檔案同步內部部署網路組態
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93681813c12f0df99909c849e57153e7a64c78fb
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299306"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 檔案同步 Proxy 和防火牆設定
Azure 檔案同步會將您的內部部署伺服器連線到 Azure 檔案服務，以啟用多網站同步處理和雲端層功能。 因此，內部部署伺服器必須連線到網際網路。 IT 系統管理員必須決定進入 Azure 雲端服務之伺服器的最佳路徑。

這篇文章會提供特定需求的深入解析，以及可以將伺服器成功且安全地連線到 Azure 檔案同步的選項。

## <a name="overview"></a>概觀
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

| 服務 | 公用雲端端點 | Azure Government 端點 | 使用量 |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | 任何使用者呼叫 (例如 PowerShell) 都會通過這個 URL，包括初始伺服器註冊呼叫。 |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | Azure Resource Manager 呼叫必須由已驗證的使用者進行。 為了獲致成功，此 URL 用於進行使用者驗證。 |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | 在部署 Azure 檔案同步時，將會在訂用帳戶的 Azure Active Directory 中建立服務主體。 針對該目的使用此 URL。 此主體用於將最小權限集合委派給 Azure 檔案同步服務。 執行 Azure 檔案同步之初始設定的使用者，必須是具有訂用帳戶擁有者權限的已驗證使用者。 |
| **Azure 儲存體** | &ast;.core.windows.net | &ast;。 core.usgovcloudapi.net | 當伺服器下載檔案時，如果伺服器可以直接與儲存體帳戶中的 Azure 檔案共用通話，就可以更有效率地執行資料移動。 伺服器具有只允許存取目標檔案共用的 SAS 金鑰。 |
| **Azure 檔案同步** | &ast;.one.microsoft.com<br>&ast;。 afs.azure.net | &ast;。 afs.azure.us | 初始伺服器註冊之後，伺服器會收到該區域中 Azure 檔案同步服務執行個體的區域 URL。 伺服器可以使用 URL 直接且有效率地與處理其同步的執行個體進行通訊。 |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | 一旦安裝了 Azure 檔案同步代理程式，系統就會使用 PKI URL 來下載與 Azure 檔案同步服務和 Azure 檔案共用通訊時所需的中繼憑證。 OCSP URL 是用來檢查憑證的狀態。 |

> [!Important]
> 當允許前往 &ast;.one.microsoft.com 的流量時，伺服器可能會有前往同步服務以外的流量。 子網域底下有許多 Microsoft 服務可用。

如果 &ast;.one.microsoft.com 太廣泛，您可只允許對 Azure 檔案同步服務的明確區域執行個體進行通訊，藉此限制伺服器的通訊。 要選擇哪個執行個體，取決於您將伺服器部署及註冊到哪個儲存體同步服務區域。 在下表中，該區域稱為「主要端點 URL」。

基於商務持續性和災害復原 (BCDR) 的理由，您可能已在異地備援 (GRS) 儲存體帳戶中指定 Azure 檔案共用。 如果情況確實如此，則在發生持久的區域中斷時，Azure 檔案共用會容錯移轉至配對的區域。 Azure 檔案同步會使用相同的區域配對作為儲存體。 因此，如果您使用 GRS 儲存體帳戶，您必須啟用其他 Url，讓您的伺服器可以與配對的區域交談，以進行 Azure 檔案同步。下表會呼叫這個「配對區域」。 此外，也必須啟用流量管理員設定檔 URL。 這可確保在發生容錯移轉時，網路流量會順暢地重新路由傳送至配對的區域，這個行為在下表中稱為「探索 URL」。

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

> - HTTPs：\//kailani.one.microsoft.com （主要端點：美國西部）
> - HTTPs：\//kailani1.one.microsoft.com （配對故障的區域：美國東部）
> - HTTPs：\//tm-kailani.one.microsoft.com （主要區域的探索 URL）

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>允許 Azure 檔案同步 IP 位址的清單
如果您的內部部署防火牆需要將特定的 IP 位址新增至允許清單以連線到 Azure 檔案同步，您可以根據您要連線的區域新增下列 IP 位址範圍。

| 區域 | IP 位址範圍 |
|--------|-------------------|
| 美國中部 | 52.176.149.179/32、20.37.157.80/29 |
| 美國東部 2 | 40.123.47.110/32、20.41.5.144/29 |
| 美國東部 | 104.41.148.238/32、20.42.4.248/29 |
| 美國中北部 | 65.52.62.167/32、40.80.188.24/29 |
| 美國中南部 | 104.210.219.252/32、13.73.248.112/29 |
| 美國西部 2 | 52.183.27.204/32、20.42.131.224/29 |
| 美國中西部 | 52.161.25.233/32、52.150.139.104/29 |
| 美國西部 | 40.112.150.67/32、40.82.253.192/29 |
| 加拿大中部 | 52.228.42.41/32、52.228.81.248/29 |
| 加拿大東部 | 52.235.36.119/32、40.89.17.232/29 |
| 巴西南部 | 191.237.253.115/32、191.235.225.216/29 |
| 北歐 | 40.113.94.67/32、20.38.85.152/29 |
| 西歐 | 104.40.191.8/32、20.50.1.0/29 |
| 法國中部 | 52.143.166.54/32、20.43.42.8/29 |
| 法國南部 | 52.136.131.99/32、51.105.88.248/29 |
| 英國南部 | 51.140.67.72/32、51.104.25.224/29 |
| 英國西部 | 51.140.202.34/32、51.137.161.240/29 |
| 瑞士北部 | 51.107.48.224/29 |
| 瑞士西部 | 51.107.144.216/29 |
| 挪威西部 | 51.120.224.216/29 |
| 挪威東部 | 51.120.40.224/29 |
| 東亞 | 23.102.225.54/32、20.189.108.56/29 |
| 東南亞 | 13.76.81.46/32、20.43.131.40/29 |
| 澳大利亞中部 | 20.37.224.216/29 |
| 澳大利亞中部 2 | 20.36.120.216/29 |
| 澳大利亞東部 | 13.75.153.240/32、20.37.195.96/29 |
| 澳大利亞東南部 | 13.70.176.196/32、20.42.227.128/29 |
| 印度南部 | 104.211.231.18/32、20.41.193.160/29 |
| 印度西部 | 52.136.48.216/29 |
| 日本東部 | 104.41.161.113/32、20.43.66.0/29 |
| 日本西部 | 23.100.106.151/32、40.80.57.192/29 |
| 南韓中部 | 52.231.67.75/32、20.41.65.184/29 |
| 南韓南部 | 52.231.159.38/32、40.80.169.176/29 |
| US DoD 東部 | 20.140.72.152/29 |
| US Gov 亞利桑那州 | 20.140.64.152/29 |
| US Gov 亞利桑那州 | 52.244.75.224/32、52.244.79.140/32 |
| US Gov 愛荷華州 | 52.244.79.140/32、52.244.75.224/32 |
| US Gov 德克薩斯州 | 52.238.166.107/32、52.238.79.29/32 |
| US Gov 維吉尼亞州 | 13.72.17.152/32、52.227.153.92/32 |
| 南非北部 | 102.133.175.72/32 |
| 南非西部 | 102.133.75.173/32、102.133.56.128/29、20.140.48.216/29 |
| 阿拉伯聯合大公國中部 | 20.45.71.151/32、20.37.64.216/29、20.140.48.216/29 |
| 阿拉伯聯合大公國北部 | 40.123.216.130/32、20.38.136.224/29、20.140.56.136/29 |

## <a name="test-network-connectivity-to-service-endpoints"></a>測試與服務端點的網路連線能力
一旦向 Azure 檔案同步服務註冊伺服器，就可以使用 StorageSyncNetworkConnectivity Cmdlet 和 Serverregistration.exe 來測試與此伺服器特定的所有端點（Url）的通訊。 此 Cmdlet 有助於疑難排解未完成的通訊，以防止伺服器完全使用 Azure 檔案同步，而且可以用來微調 proxy 和防火牆設定。

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
