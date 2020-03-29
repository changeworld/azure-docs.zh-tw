---
title: 在 Azure 資料工廠中排除自託管集成運行時故障
description: 瞭解如何在 Azure 資料工廠中解決自託管的集成運行時問題。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439881"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>排除自託管集成運行時故障

本文探討了 Azure 資料工廠中自託管集成運行時的常見故障排除方法。

## <a name="common-errors-and-resolutions"></a>常見錯誤和解決方案

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>錯誤訊息：自託管集成運行時無法連接到雲服務

![自託管 IR 連接問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自託管的集成運行時無法連接到資料工廠服務（後端）。 此問題通常是由防火牆中的網路設置引起的。

#### <a name="resolution"></a>解決方案

1. 檢查集成運行時服務是否正在運行。
    
   ![自託管 IR 服務運行狀態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果服務正在運行，則繼續執行步驟 3。

1. 如果在自託管集成運行時（預設設置）上未配置代理，則在安裝自託管集成運行時的電腦上運行以下 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 服務 URL 可能因數據工廠位置而異。 您可以在**ADF UI** > **連接** > **集成運行時** > 下找到服務 URL**編輯自託管的 IR** > **節點** > **查看服務 URL**。
            
    以下是預期的回應：
            
    ![PowerShell 命令回應](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 如果您沒有收到預期的回應，請使用以下方法之一，以適合您的情況：
            
    * 如果您收到"遠端名稱無法解決"消息，則存在網域名稱系統 （DNS） 問題。 請與您的網路團隊聯繫以解決此問題。
    * 如果收到"ssl/tls 證書不受信任"消息，請檢查 其https://wu2.frontend.clouddatahub.net/證書在電腦上是否受信任，然後使用證書管理器安裝公共證書。 此操作應緩解此問題。
    * 轉到**Windows** > **事件檢視器（日誌）** > **應用程式和服務日誌** > **集成運行時**，並檢查由 DNS、防火牆規則或公司網路設置引起的任何故障。 （如果您發現此類故障，則強制關閉連接。由於每個公司都有自訂的網路設置，請與網路團隊聯繫以解決這些問題。

1. 如果在自託管集成運行時配置了"代理"，請驗證代理伺服器是否可以訪問服務終結點。 有關示例命令，請參閱[PowerShell、Web 請求和代理](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)。    
                
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
            
![電源殼命令回應 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 代理注意事項：
> * 檢查是否需要將代理伺服器放在安全收件者清單中。 如果是這樣，請確保[這些域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)位於安全收件者清單中。
> * 檢查 TLS/SSL 憑證"wu2.frontend.clouddatahub.net/"是否受代理伺服器上信任。
> * 如果您在代理上使用 Active Directory 身份驗證，請將服務帳戶更改為可以訪問代理的使用者帳戶，作為"集成運行時服務"。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>錯誤訊息：自託管集成運行時節點/邏輯 SHIR 處於非活動/"正在運行（受限）"狀態

#### <a name="cause"></a>原因 

自託管的集成運行時節點可能具有**非活動**狀態，如下圖所示：

![非活動自託管紅外節點](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

當節點無法相互通信時，將發生此行為。

#### <a name="resolution"></a>解決方案

1. 登錄到節點託管的 VM。 在**應用程式和服務日誌** > **集成運行時**、打開事件檢視器和篩選所有錯誤日誌下。

1. 檢查錯誤日誌是否包含以下錯誤： 
    
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
        
1.  Check whether the error log contains the following:

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
