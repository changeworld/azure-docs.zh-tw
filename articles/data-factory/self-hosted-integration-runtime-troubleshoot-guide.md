---
title: 針對在 Azure Data Factory 中的自我裝載整合執行階段進行疑難排解
description: 深入了解如何針對在 Azure Data Factory 中的自我裝載整合執行階段進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/19/2020
ms.author: abnarain
ms.openlocfilehash: 521756081db938e749849e6f3630dbd60700d24f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023809"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>針對自我裝載整合執行階段進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將探討在 Azure Data Factory 中自我裝載整合執行階段的常見疑難排解方法。

## <a name="gather-self-hosted-integration-runtime-logs-from-azure-data-factory"></a>從 Azure Data Factory 收集自我裝載整合執行時間記錄

針對在自我裝載 IR/共用 IR 上執行的失敗活動，Azure Data Factory 支援「查看」和「上傳錯誤記錄」。 您可以遵循下列步驟來取得錯誤報表識別碼，然後輸入報告識別碼來找出相關的已知問題。

1. 移至 [**活動執行**] 頁面。

1. 在 [**錯誤**] 資料行底下，按一下 [下方] 按鈕。

    ![[活動執行] 頁面](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 您會看到失敗活動執行的相關記錄檔。 按一下 [**傳送記錄**檔] 按鈕以取得進一步的協助。

    ![傳送記錄檔](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 您可以選擇您想要傳送的記錄檔。 對於*自我裝載 ir*，您可以上傳與 [失敗的活動] 或自我裝載 ir 節點上的所有記錄相關的記錄。 針對*共用 IR*，您只能上傳與失敗活動相關的記錄檔。

    ![選擇記錄](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 當記錄上傳時，如果您需要進一步協助來解決問題，請保留報告識別碼的記錄。

    ![上傳記錄檔](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 記錄檔的查看和上傳要求將會在所有線上的自我裝載 IR 實例上執行。 請確定所有自我裝載 IR 實例都在線上，以防任何遺失的記錄檔。 


## <a name="common-errors-and-resolutions"></a>常見錯誤和解決方案

### <a name="error-message"></a>錯誤訊息： 

`Self-hosted integration runtime can't connect to cloud service`

![自我裝載整合執行階段連線問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自我裝載整合執行階段無法連線到 Data Factory 服務 (後端)。 此問題通常是因為防火牆中的網路設定所造成。

#### <a name="resolution"></a>解決方案

1. 檢查整合執行階段服務是否正在執行。
    
   ![自我裝載整合執行階段服務執行狀態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果此服務正在執行，請移至步驟 3。

1. 如果未在自我裝載整合執行階段 (預設設定) 上設定 Proxy，請在安裝自我裝載整合執行階段的機器上執行下列 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 服務 URL 可能有所不同，視您的 Data Factory 位置而定。 您可以在 [ADF UI]  > [連線] > [整合執行階段] >  [編輯自我裝載整合執行階段] > [節點] > [檢視服務 URL]。
            
    預期的回應如下：
            
    ![PowerShell 命令回應](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 若您未收到預期的回應，請視適合您的情況，使用下列其中一種方法：
            
    * 如果您收到「無法解析遠端名稱」訊息，即表示有網域名稱系統 (DNS) 問題。 請連絡您的網路小組以修正此問題。
    * 如果您收到「ssl/tls 憑證不受信任」訊息，請檢查機器上是否有信任 https://wu2.frontend.clouddatahub.net/ 的憑證，然後使用憑證管理員安裝公開憑證。 這個動作應可減輕此問題。
    * 移至 [Windows] > [事件檢視器 (記錄)] > [應用程式和服務記錄] > [整合執行階段]，然後查看是否有任何由 DNS、防火牆規則或公司網路設定所造成的失敗。 (如果您發現此類錯誤，請強制關閉連線。)由於每間公司都有自訂的網路設定，請連絡您的網路小組以針對這些問題進行疑難排解。

1. 如果您已在自我裝載整合執行階段上設定「Proxy」，請確認您的 Proxy 伺服器可以存取服務端點。 如需範例命令，請參閱 [PowerShell、Web 要求和 Proxy](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)。    
                
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

預期的回應如下：
            
![PowerShell 命令回應 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy 考量：
> *    檢查是否需要將 Proxy 伺服器放在安全的收件者清單中。 若是如此，請確定[這些網域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) 列於安全收件者的清單上。
> *    檢查 Proxy 伺服器上的 TLS/SSL 憑證「wu2.frontend.clouddatahub.net/」是否受信任。
> *    如果您在 Proxy 上使用 Active Directory 驗證，請將服務帳戶變更為可將 Proxy 作為「整合執行階段服務」存取的使用者帳戶。

### <a name="error-message"></a>錯誤訊息： 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>原因 

自我裝載的整合執行階段節點可能會有**非使用中**的狀態，如下列螢幕擷取畫面所示：

![非使用中的自我裝載整合執行階段節點](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

當節點無法彼此通訊時，即會發生這種行為。

#### <a name="resolution"></a>解決方案

1. 登入節點裝載的 VM。 在 [應用程式和服務記錄檔] > [整合執行階段]下，開啟 [事件檢視器]，然後篩選所有錯誤記錄檔。

1. 檢查錯誤記錄檔是否包含下列錯誤訊息： 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 如果您看到此錯誤，請在命令列上執行下列程式碼： 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 如果您收到下列錯誤，請洽詢您的 IT 部門，以取得協助來解決此問題。 成功 telnet 之後，如果您仍有整合式執行時間節點狀態的問題，請洽詢 Microsoft 支援服務。
        
   ![命令列錯誤](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 檢查錯誤記錄檔是否包含下列內容：

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 若要解決此問題，請嘗試下列其中一種或兩種方法：
    - 將所有節點放在相同的網域中。
    - 在所有託管 VM 的主機檔案中，新增 IP 至主機的對應。


## <a name="troubleshoot-connectivity-issue"></a>針對連線問題進行疑難排解

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>針對自我裝載 IR 與 Data Factory 或自我裝載 IR 和資料來源/接收器之間的連線問題進行疑難排解

若要疑難排解網路連線問題，您應該先瞭解如何[收集網路追蹤](#how-to-collect-netmon-trace)、瞭解如何使用它，以及如何在從自我裝載 IR 在實際情況下套用 netmon 工具之前，先[分析 netmon 追蹤](#how-to-analyze-netmon-trace)。

有時候，當我們針對連線問題進行疑難排解時，例如，在自我裝載 IR 與 Data Factory 之間，如下所示： 

![HTTP 要求失敗](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

或者，在自我裝載 IR 和資料來源/接收之間，我們會遇到下列錯誤：

**錯誤訊息：**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**錯誤訊息：**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**解決方式：** 遇到上述問題時，請參閱下列指示以進一步進行疑難排解：

取得 netmon 追蹤並進一步分析。
- 首先，您可以設定篩選器，以查看從伺服器到用戶端的任何重設。 從下面的範例中，您可以看到伺服器端是 Data Factory server。

    ![Data factory 伺服器](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- 當您取得重設套件時，您可以遵循 TCP 來尋找交談。

    ![尋找交談](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 接著，您可以藉由移除篩選準則來取得用戶端與 Data Factory 伺服器之間的轉換。

    ![取得對話](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 根據所收集的 netmon 追蹤，我們可以告知 TTL （TimeToLive）總計為64。 根據本文中提及的**預設 TTL 和躍點限制值** [（如下](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)所述），我們可以看到它是重設套件並導致中斷連線的 Linux 系統。

    預設的 TTL 和躍點限制值會因不同的作業系統而異，以下是一些預設值：
    - Linux 核心2.4 （約2001）：255適用于 TCP、UDP 和 ICMP
    - Linux 核心4.10 （2015）： TCP、UDP 和 ICMP 的64
    - Windows XP （2001）： TCP、UDP 和 ICMP 的128
    - Windows 10 （2015）： TCP、UDP 和 ICMP 的128
    - Windows Server 2008： TCP、UDP 和 ICMP 的128
    - Windows Server 2019 （2018）： TCP、UDP 和 ICMP 的128
    - macOS （2001）：64，適用于 TCP、UDP 和 ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    不過，在上述範例中，它會顯示為61，而不是64，因為當網路套件到達目的地時，它需要經過不同的躍點，例如路由器/網路裝置。 將會在最終 TTL 中扣除路由器/網路裝置的數目。
    在此情況下，我們可以看到重設可能會從具有 TTL 64 的 Linux 系統傳送。

- 我們需要檢查來自自我裝載 IR 的第四個躍點，以確認重設裝置的來源。
 
    *Linux 系統 A 的網路套件與 TTL 64-> B TTL 64 減 1 = 63-> C TTL 63 減去 1 = 62-> TTL 62 減 1 = 61 自我裝載 IR*

- 在理想的情況下，TTL 將是128，這表示 Windows 系統正在執行我們的 Data Factory。 如下列範例所示， *128 – 107 = 21 個躍點*，表示封裝的21個躍點是在 TCP 3 交握期間從 Data Factory 傳送至自我裝載 IR。
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    因此，您必須接洽網路小組，檢查第四個躍點來自自我裝載 IR。 如果它是做為 Linux 系統的防火牆，請檢查是否有裝置在 TCP 3 交握之後重設套件的記錄。 不過，如果您不確定要在哪裡進行調查，請嘗試在有問題的時間內，從自我裝載的 IR 和防火牆取得 netmon 追蹤，以找出哪些裝置可能會重設此套件，並導致中斷連接。 在此情況下，您也需要讓網路小組繼續進行。

### <a name="how-to-collect-netmon-trace"></a>如何收集 netmon 追蹤

1.  從[這個網站](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)下載 Netmon 工具，並將它安裝在您的伺服器電腦上（發生問題的伺服器）和用戶端（例如自我裝載 IR）。

2.  建立資料夾，例如，在下列路徑中： *D:\netmon*。 請確定它有足夠的空間來儲存記錄檔。

3.  捕獲 IP 和埠資訊。 
    1. 啟動 CMD 命令提示字元。
    2. 選取 [以系統管理員身分執行] 並執行下列命令：
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  捕獲 Netmon 追蹤（網路套件）。
    1. 啟動 CMD 命令提示字元。
    2. 選取 [以系統管理員身分執行] 並執行下列命令：
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. 您可以使用三種不同的命令來捕捉網路頁面：
        - 選項 A： RoundRobin File 命令（這只會捕捉一個檔案，而且會覆寫舊的記錄檔）。

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - 選項 B：連鎖的檔案命令（如果達到 200 MB，則會建立新的檔案）。
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - 選項 C：已排程的檔案命令。

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  按**Ctrl + C**停止捕捉 Netmon 追蹤。
 
> [!NOTE]
> 如果您只能在用戶端電腦上收集 netmon 追蹤，請取得伺服器 ip 位址，以協助您分析追蹤。

### <a name="how-to-analyze-netmon-trace"></a>如何分析 netmon 追蹤

當您嘗試使用已收集的以上 netmon 追蹤來進行 telnet **8.8.8.8 888**時，您應該會看到下列追蹤：

![netmon 追蹤1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon 追蹤2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

這表示您無法根據埠**888**對**8.8.8.8**伺服器端進行 TCP 連線，因此您會在該處看到兩個**SynReTransmit**額外的套件。 因為來源**自我 host2.contoso.com**無法連線到第一個封裝的**8.8.8.8** ，所以它會繼續進行連接。

> [!TIP]
> - 您可以按一下 [**載入篩選器**] [  ->  **標準篩選器**] [  ->  **位址**  ->  **IPv4 位址**]。
> - 輸入**IPv4. Address = = 8.8.8.8**作為篩選準則，然後**按一下 [** 套用]。 之後，您只會看到從本機電腦到目的地**8.8.8.8**的通訊。

![篩選位址1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![篩選位址2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

以下範例顯示良好案例的樣子。 

- 如果 Telnet **8.8.8.8 53**在沒有任何問題的情況下運作正常，您可以看到 tcp 3 交握髮生，然後會話會完成 tcp 4 交握。

    ![良好案例範例1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![良好案例範例2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 根據上述的 TCP 3 交握，您可以看到下列工作流程：

    ![TCP 3 交握工作流程](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 完成會話的 TCP 4 交握，其工作流程將顯示如下：

    ![TCP 4 交握](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 交握工作流程](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>接下來的步驟

如需疑難排解的詳細協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [對應資料流程效能指南](concepts-data-flow-performance.md)
