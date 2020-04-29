---
title: 針對 Azure Data Factory 中的自我裝載整合執行時間進行疑難排解
description: 瞭解如何針對 Azure Data Factory 中的自我裝載整合執行時間問題進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414883"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>針對自我裝載整合執行時間進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中自我裝載整合執行時間的常見疑難排解方法。

## <a name="common-errors-and-resolutions"></a>常見錯誤和解決方案

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>錯誤訊息：自我裝載整合執行時間無法連接到雲端服務

![自我裝載 IR 連接問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自我裝載整合執行時間無法連接到 Data Factory 服務（後端）。 此問題通常是因為防火牆中的網路設定所造成。

#### <a name="resolution"></a>解決方案

1. 檢查 integration runtime 服務是否正在執行。
    
   ![自我裝載 IR 服務執行狀態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果服務正在執行，請移至步驟3。

1. 如果未在自我裝載整合執行時間（這是預設設定）上設定 proxy，請在安裝自我裝載整合執行時間的電腦上執行下列 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 服務 URL 可能有所不同，視您的 Data Factory 位置而定。 您可以在**ADF UI** > **Connections** > **連線整合運行** > 時間下，找到服務 url**編輯自我裝載 IR** > **節點** > **View service url**。
            
    以下是預期的回應：
            
    ![PowerShell 命令回應](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 如果您未收到預期的回應，請視情況使用下列其中一種方法：
            
    * 如果您收到「無法解析遠端名稱」訊息，則會有一個網域名稱系統（DNS）問題。 請洽詢您的網路小組以修正此問題。
    * 如果您收到「ssl/tls 憑證不受信任」訊息，請檢查電腦上的憑證https://wu2.frontend.clouddatahub.net/是否受信任，然後使用憑證管理員安裝公開憑證。 此動作應可減輕此問題。
    * 移至 **[Windows** > **事件檢視器（記錄）** > ] [**應用程式及服務記錄** > 檔]**Integration Runtime** ，並檢查是否有任何因 DNS、防火牆規則或公司網路設定而造成的失敗。 （如果您發現這類失敗，請強制關閉連接）。因為每一家公司都有自訂的網路設定，請洽詢您的網路小組來疑難排解這些問題。

1. 如果已在自我裝載整合執行時間上設定 "proxy"，請確認您的 proxy 伺服器可以存取服務端點。 如需範例命令，請參閱[PowerShell、web 要求和](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)proxy。    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

以下是預期的回應：
            
![Powershell 命令回應2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy 考慮：
> *    檢查是否需要將 proxy 伺服器放在安全的收件者清單中。 若是如此，請確定[這些網域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)位於安全的收件者清單中。
> *    檢查 proxy 伺服器上的 TLS/SSL 憑證 "wu2.frontend.clouddatahub.net/" 是否受信任。
> *    如果您在 proxy 上使用 Active Directory 驗證，請將服務帳戶變更為可存取 proxy 的使用者帳戶（「Integration Runtime 服務」）。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>錯誤訊息：自我裝載整合執行時間節點/邏輯 SHIR 處於非作用中/「執行中（有限制）」狀態

#### <a name="cause"></a>原因 

自我裝載整合執行時間節點可能會有**非**作用中狀態，如下列螢幕擷取畫面所示：

![非使用中的自我裝載 IR 節點](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

當節點無法彼此通訊時，就會發生這種行為。

#### <a name="resolution"></a>解決方案

1. 登入節點主控的 VM。 在 [**應用程式及服務記錄** > 檔] 底下**Integration Runtime**，開啟事件檢視器，然後篩選所有錯誤記錄檔。

1. 檢查錯誤記錄檔是否包含下列錯誤： 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
