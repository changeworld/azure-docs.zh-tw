---
title: 針對在 Azure Data Factory 中的自我裝載整合執行階段進行疑難排解
description: 深入了解如何針對在 Azure Data Factory 中的自我裝載整合執行階段進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: abnarain
ms.openlocfilehash: f0957b74bf13acfcc80e38cccaec389fbbd19fa0
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131293"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>針對自我裝載整合執行階段進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將探討在 Azure Data Factory 中自我裝載整合執行階段的常見疑難排解方法。

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>從 Azure Data Factory 收集自我裝載的 IR 記錄檔

針對在自我裝載 IR/共用 IR 上執行的失敗活動，Azure Data Factory 支援查看和上傳錯誤記錄檔。 您可以遵循下列步驟來取得錯誤報表識別碼，然後輸入報表識別碼以找出相關的已知問題。

1. 移至 [ **活動執行** ] 頁面。

1. 在 [ **錯誤** ] 資料行底下，按一下 [下方] 按鈕。

    ![活動執行頁面](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 您會看到失敗的活動執行的相關記錄。 按一下 [ **傳送記錄** 檔] 按鈕以取得進一步協助。

    ![傳送記錄檔](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 您可以選擇想要傳送的記錄檔。 針對 *自我裝載 ir*，您可以上傳與失敗活動相關的記錄，或自我裝載 ir 節點上的所有記錄。 針對 *共用 IR*，您只能上傳與失敗活動相關的記錄。

    ![選擇記錄檔](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 當記錄上傳時，如果您需要進一步協助來解決問題，請保留報表識別碼的記錄。

    ![上傳記錄](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 記錄檔的查看和上傳要求將在所有線上自我裝載 IR 實例上執行。 請確定所有自我裝載 IR 實例都在線上，以防遺失任何記錄。 


## <a name="self-hosted-ir-general-failure-or-error"></a>自我裝載 IR 常見失敗或錯誤

### <a name="tlsssl-certificate-issue"></a>TLS/SSL 憑證問題

#### <a name="symptoms"></a>徵兆

嘗試從「自我裝載 IR 設定管理員」 -> 「從內部網路進行遠端存取」啟用 TLS/SSL 憑證 (進階) 時，在選取 TLS/SSL 憑證後顯示下列錯誤：

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

在上述情況下，使用者會使用具有 "microsoft.com" 的憑證作為最後一個項目。

#### <a name="cause"></a>原因

這是 WCF 的已知問題：WCF TLS/SSL 驗證只會檢查 SAN 中的最後一個 DNSName。 

#### <a name="resolution"></a>解決方法

Azure Data Factory v2 自我裝載 IR 支援萬用字元憑證。 此問題通常是因為 SSL 憑證不正確而發生。 SAN 中的最後一個 DNSName 應該是有效的。 請遵循下列步驟來驗證。 
1.  開啟管理主控台，再從憑證詳細資料中，再次檢查 *主體* 和 *主體替代名稱* 。 例如，在上述案例中， *主體別名*中的最後一個專案是「DNS 名稱 = microsoft.com.com」，並不合法。
2.  請洽詢憑證問題公司以移除錯誤的 DNS 名稱。

### <a name="concurrent-jobs-limit-issue"></a>並行作業數限制問題

#### <a name="symptoms"></a>徵兆

當嘗試增加 Azure Data Factory UI 的並行作業限制數時，系統會像執行「更新」一樣永遠沒有回應。
並行作業數的最大值設定為 24，而您需要增加計數，以便更快速地執行作業。 您可以輸入的最小值是 3，最大值為 32。 您將值從24增加到32，並點擊 [ *更新* ] 按鈕，在 UI 中，它會停滯于 *更新* ，如下所示。 重新整理之後，客戶仍然看到值為 24，而且永遠不會更新為 32。

![正在更新狀態](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>原因

此設定有一項限制，因為此值取決於電腦 logicCore 和記憶體，您只能將其調整為較小的值 (例如24) 並查看結果。

> [!TIP] 
> - 如需邏輯核心計數的詳細資訊，以及如何尋找機器的邏輯核心計數，請參閱 [這篇文章](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)。
> - 如需如何計算 math 的詳細資訊，請參閱 [這篇文章](https://www.rapidtables.com/calc/math/Log_Calculator.html)。


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>自我裝載 IR HA SSL 憑證問題

#### <a name="symptoms"></a>徵兆

自我裝載 IR 工作節點已回報下列錯誤：

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>原因

處理與 SSL/TLS 交握相關的案例時，可能會遇到一些與憑證鏈結驗證相關的問題。 

#### <a name="resolution"></a>解決方法

- 以下是針對 x.509 憑證鏈組建失敗進行疑難排解的快速且直覺的方式。
 
    1. 匯出需要驗證的憑證。 移至管理電腦憑證並尋找您要檢查的憑證，以滑鼠右鍵按一下 [所有工作] -> [匯出]。
    
        ![匯出工作](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. 將匯出的憑證複製到用戶端電腦。 
    3. 在用戶端上的 CMD 執行下列命令。 請確定您已 *\<certificate path>* 使用相關的路徑取代以下和 *\<output txt file path>* 預留位置。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        例如：

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 檢查輸出 txt 檔案中是否有任何錯誤。 您可以在 txt 檔案結尾處找到錯誤摘要。

        例如： 

        ![錯誤摘要](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        如果您在記錄檔結尾沒有看到任何錯誤（如下所示），您可以考慮在用戶端電腦上成功建立憑證鏈。
        
        ![記錄檔中沒有錯誤](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- 如果憑證檔案中已設定 AIA、CDP 和 OCSP。 我們可以用更直覺的方式來檢查它。
 
    1. 您可以藉由檢查憑證的詳細資料來取得此資訊。
    
        ![憑證詳細資料](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. 執行下列命令。 請確定您已將預留位置取代為 *\<certificate path>* 憑證的相關路徑。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. 然後隨即開啟「URL 擷取工具」。 您可以按一下 [擷取] 按鈕，確認來自 AIA、CDP 和 OCSP 的憑證。

        ![抓取按鈕](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        如果來自 AIA 的憑證狀態為「已驗證」，而且來自 CDP 或 OCSP 的憑證狀態為「已驗證」，則表示已成功建置憑證鏈結。

        如果您在擷取 AIA、CDP 時看到失敗，請與網路小組合作，協助用戶端電腦準備好連線到目標 URL。 如果可以驗證 http 路徑或 ldap 路徑，就應該會成功。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>自我裝載 IR 無法載入檔案或組件

#### <a name="symptoms"></a>徵兆

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
例如： 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>原因

如果您採用進程監視器，您可以看到下列結果：

[![進程監視器](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 您可以設定篩選器，如下列螢幕擷取畫面所示。
> 它會告訴我們 dll **ValueTuple** 不在 GAC 相關資料夾或 *C:\Program Files\Microsoft integration Runtime\4.0\Gateway*或 *C:\Program Files\Microsoft integration Runtime\4.0\Shared* 資料夾中。
> 基本上，檔案會先從 GAC 資料夾載入 dll，然後從「共用」載入 dll，最後再從「閘道」資料夾載入 dll。 因此，您可以將 dll 放到任何可能有幫助的路徑。

![設定篩選](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>解決方法

您可以找到 **System.ValueTuple.dll** 位於 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* 資料夾。 將 **System.ValueTuple.dll** 複製到 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 資料夾，以解決此問題。

您可以使用相同的方法來解決其他檔案或元件遺漏的問題。

#### <a name="more-information"></a>相關資訊

您在 *%windir%\Microsoft.NET\assembly* 和 *%windir%\assembly* 下看到 System.ValueTuple.dll 的原因是 .net 的行為。 

從下面的錯誤中，您可以清楚地看到元件 *ValueTuple* 。 因此，當應用程式嘗試檢查元件 *System.ValueTuple.dll*時，就會發生這類問題。
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
如需 GAC 的詳細資訊，請參閱 [這篇文章](https://docs.microsoft.com/dotnet/framework/app-domains/gac)。


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>如何稽核自我裝載 IR 金鑰遺漏的問題

#### <a name="symptoms"></a>徵兆

自我裝載整合執行階段因為沒有金鑰突然離線，事件記錄檔中顯示下列錯誤訊息：`Authentication Key is not assigned yet`

![缺少驗證金鑰](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>原因

- 已刪除自我裝載的 IR 節點或入口網站中的邏輯自我裝載 IR。
- 已完成完整的解除安裝作業。

#### <a name="resolution"></a>解決方法

如果上述任一原因都不適用，您可以移至資料夾： *%Programdata%\Microsoft\Data Transfer\DataManagementGateway*，並檢查名稱為 [設定] 的檔案 **是否已刪除** 。 如果已刪除，請依照[這裡](https://www.netwrix.com/how_to_detect_who_deleted_file.html)的指示，稽核刪除該檔案的人員。

![檢查設定檔案](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>無法使用自我裝載 IR 來橋接兩個內部部署資料存放區

#### <a name="symptoms"></a>徵兆

建立來源和目的地資料存放區的自我裝載 IR 後，您想將兩個 IR 連接在一起，以完成複本。 如果資料存放區是在不同的 Vnet 中設定，或無法瞭解閘道機制，您將會遇到類似以下的錯誤： *無法在目的地 IR 中找到來源的驅動程式*; *目的地 IR 無法存取來源*。
 
#### <a name="cause"></a>原因

自我裝載 IR 設計為複製活動的中央節點，而不是每個資料存放區都需要安裝的用戶端代理程式。
 
在上述情況下，每個資料存放區的連結服務都應該使用相同的 IR 建立，而該 IR 應該能夠透過網路存取這兩個資料存放區。 無論 IR 是安裝在來源資料存放區、目的地資料存放區或第三部電腦上，如果有兩個連結服務是使用不同的 IR 建立的，但在相同的複製活動中使用，則會使用目的地 IR，而且這兩個資料存放區的驅動程式必須安裝在目的地 IR 電腦上。

#### <a name="resolution"></a>解決方法

將來源和目的地驅動程式安裝在目的地 IR 上，並確定其可以存取來源資料存放區。
 
如果流量無法在兩個資料存放區之間的網路傳遞 (例如資料存放區是在兩個 VNET 中設定)，您可能無法在一個活動中完成複製，即使已安裝 IR 也一樣。 在這種情況下，您可以在個別的 VNET 中建立兩個具有兩個 IR 的複製活動：一個是從資料存放區 1 複製到 Azure Blob 儲存體的整合執行階段，另一個從 Azure Blob 儲存體複製到資料存放區 2。 這可能會模擬使用整合執行階段來建立連線兩個已中斷連線資料存放區之橋接器的需求。


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>認證同步處理問題導致 HA 遺失認證

#### <a name="symptoms"></a>徵兆

已從目前的 Integration Runtime 節點刪除資料來源認證 "XXXXXXXXXX"，承載為「當您刪除 Azure 入口網站上的連結服務，或工作的承載錯誤時，請使用您的認證，重新建立新的連結服務」。

#### <a name="cause"></a>原因

您的自我裝載整合執行階段是以具有兩個節點的 HA 模式建立，但這兩者的狀態並非認證同步，這表示儲存在發送器節點中的認證不會同步處理至其他背景工作角色節點。 如果從發送器節點到背景工作節點發生任何容錯移轉，但認證只存在於先前的發送器節點中，則當嘗試存取認證時，工作將會失敗，而且您會遇到上述錯誤。

#### <a name="resolution"></a>解決方法

避免此問題的唯一方法是確保兩個節點都處於認證同步狀態。 否則，您必須重新針對新發送器輸入認證。


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>因為缺少私密金鑰，因此無法選擇憑證

#### <a name="symptoms"></a>徵兆

1.  將 PFX 檔案匯入至憑證存放區。
2.  透過 IR Configuration Manager UI 選取憑證時，您會遇到下列錯誤：

    ![私密金鑰遺失](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>原因

- 使用者帳戶權限太低，無法存取私密金鑰。
- 憑證是以簽章的形式產生，並非作為金鑰交換。

#### <a name="resolution"></a>解決方法

1.  使用可存取私密金鑰的特殊權限帳戶來操作 UI。
2.  執行下列命令以匯入憑證：
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>自我裝載 IR 設定

### <a name="the-integration-runtime-registration-error"></a>Integration Runtime 的註冊錯誤 

#### <a name="symptoms"></a>徵兆

有時候我們會想要在不同的帳戶中執行自我裝載 IR，原因如下：
- 公司原則不允許服務帳戶。
- 需要進行某些驗證。

變更服務面板中的服務帳戶之後，您可能會發現 Integration Runtime 停止運作。

![IR 註冊錯誤](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>原因

有許多資源只會授與服務帳戶。 將服務帳戶變更為另一個帳戶時，所有相依資源的許可權都會保持不變。

#### <a name="resolution"></a>解決方法

移至 Integration Runtime 事件記錄檔以檢查錯誤。

![IR 事件記錄檔](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

如果錯誤顯示為上述 *system.unauthorizedaccessexception*，請遵循下列指示：


1. 檢查 Windows 服務面板中的 *DIAHostService* logon service 帳戶。

    ![登入服務帳戶](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. 檢查登入服務帳戶是否具有資料夾的 R/W 許可權： *%programdata%\Microsoft\DataTransfer\DataManagementGateway*。

    - 根據預設，如果服務登入帳戶尚未變更，它應該具有 R/W 的許可權。

        ![服務許可權](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - 如果您已變更服務登入帳戶，請依照下列步驟來緩和問題：
        1. 清除 [將目前的自我裝載 IR 卸載]。
        1. 安裝自我裝載 IR 位。
        1. 請遵循下列指示來變更服務帳戶： 
            1. 移至 selfhosted IR 的安裝資料夾，切換到下列資料夾： *Microsoft Integration Runtime\4.0\Shared*。
            1. 使用較高的許可權啟動命令列。 *\<user>* 將和取代 *\<password>* 為您自己的使用者名稱和密碼，然後執行下列命令：
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. 如果您想要變更為 LocalSystem 帳戶，請務必使用此帳戶的正確格式。 以下是正確格式的範例：

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                請勿 **使用格式** ，如下所示：

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. 替代方式是，由於本機系統的許可權高於系統管理員，因此您也可以直接在 [服務] 中變更它。
            1. 您可以使用本機/網域使用者作為 IR 服務登入帳戶。            
        1. 註冊 Integration Runtime。

如果錯誤顯示為： *服務 ' Integration Runtime service ' (DIAHostService) 無法啟動。確認您有足夠的許可權可以啟動系統服務*，請遵循下列指示：

1. 檢查 Windows 服務面板中的 *DIAHostService* logon service 帳戶。
   
    ![登入服務帳戶](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. 檢查登入服務帳戶是否具有啟動 Windows 服務的「 **以服務方式登** 入」許可權：

    ![以服務方式登入](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>相關資訊

如果您的案例中不適用上述兩種解析度模式，請嘗試收集下列 Windows 事件記錄檔： 
- 應用程式和服務記錄檔-> Integration Runtime
- Windows 記錄-> 應用程式

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>找不到註冊按鈕以註冊自我裝載 IR    

#### <a name="symptoms"></a>徵兆

註冊自我裝載 IR 時，在 Configuration Manager UI 上找不到 **註冊** 按鈕。

![沒有註冊按鈕](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>原因

自 *Integration Runtime 3.0*版起，已移除現有 Integration Runtime 節點上的 [ **註冊** ] 按鈕，以啟用更清楚且更安全的環境。 若某個節點已註冊到某個 Integration Runtime (無論是否為線上)，若要將該節點重新註冊至另一個 Integration Runtime，您必須先解除安裝舊有的節點，再安裝並註冊該節點。

#### <a name="resolution"></a>解決方法

1. 移至 [控制台] 卸載現有的 Integration Runtime。

    > [!IMPORTANT] 
    > 在以下程式中，選取 [是]。 請勿在卸載過程中保留資料。

    ![刪除資料](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. 如果您沒有整合執行時間安裝程式 MSI，請前往 [下載中心](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) 下載最新的 Integration Runtime。
1. 安裝 MSI 並註冊 Integration Runtime。


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>因 localhost 而無法註冊自我裝載 IR    

#### <a name="symptoms"></a>徵兆

Get_LoopbackIpOrName 時，無法在新電腦上註冊自我裝載 IR。

**Debug：** 發生執行階段錯誤。
' DataTransfer. DIAgentHost. DataSourceCache ' 的類型初始化運算式擲回例外狀況。
資料庫查閱期間發生無法復原的錯誤。
 
**例外狀況詳細資料：** System.typeinitializationexception： ' Microsoft. DataTransfer. DIAgentHost. DataSourceCache ' 的類型初始化運算式擲回例外狀況。 ---> >socketexception：在系統 GetAddrInfo 的資料庫查閱期間，發生無法復原的錯誤。 (字串名稱) 。

#### <a name="cause"></a>原因

解決 localhost 時通常會發生此問題。

#### <a name="resolution"></a>解決方法

使用 Localhost 127.0.0.1 來裝載檔案並解決這類問題。


### <a name="self-hosted-setup-failed"></a>自我裝載設定失敗    

#### <a name="symptoms"></a>徵兆

無法卸載現有的 IR，或安裝新的 ir，或將現有的 IR 升級至新的 IR。

#### <a name="cause"></a>原因

安裝取決於 Windows Installer 服務。 有可能造成安裝問題的變異原因：
- 磁碟空間不足
- 缺少許可權
- NT 服務因為某些原因而遭到鎖定
- CPU 使用率過高
- MSI 檔案裝載于較慢的網路位置
- 某些系統檔案或登錄不慎觸及


## <a name="self-hosted-ir-connectivity-issues"></a>自我裝載 IR 連接問題

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>自我裝載整合執行時間無法連接到雲端服務

#### <a name="symptoms"></a>徵兆

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

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>錯誤訊息：自我裝載整合執行階段節點 / 邏輯 SHIR 處於非使用中 /「執行中 (有限制)」的狀態

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
       
1. 如果您看到此錯誤，請在命令列上執行下列命令： 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 如果您收到下列錯誤，請洽詢您的 IT 部門，以取得修正此問題的協助。 順利完成 telnet 之後，如果您仍然有整合式執行時間節點狀態的問題，請聯絡 Microsoft 支援服務。
        
   ![命令列錯誤](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 檢查錯誤記錄檔是否包含下列內容：

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 若要解決此問題，請嘗試下列其中一種或兩種方法：
    - 將所有節點放在相同的網域中。
    - 在所有託管 VM 的主機檔案中新增 IP 至主機的對應。

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>自我裝載 IR 與 Data Factory 之間的連線能力問題，或自我裝載的 IR 與資料來源/接收

若要疑難排解網路連線問題，您應該知道如何收集網路追蹤、瞭解如何使用它，以及如何在從自我裝載 IR 的真實案例中套用 Netmon 工具之前，先 [分析 netmon 追蹤](#how-to-analyze-netmon-trace) 。

#### <a name="symptoms"></a>徵兆

有時候，我們會在自我裝載 IR 和 Data Factory 之間的連線問題進行疑難排解，例如下列各項： 

![HTTP 要求失敗](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

或自我裝載 IR 與資料來源/接收之間的資料，我們將會遇到下列錯誤：

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>解決方案：

遇到上述問題時，請參閱下列指示以進一步進行疑難排解：

進行 netmon 追蹤並進一步分析。
- 首先，您可以設定篩選器，以查看從伺服器到用戶端的任何重設。 從下列範例中，您可以看到伺服器端是 Data Factory server。

    ![Data factory 伺服器](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- 當您取得重設套件時，您可以透過下列 TCP 來尋找交談。

    ![尋找對話](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 然後，您可以藉由移除篩選來取得用戶端與 Data Factory 伺服器之間的轉換。

    ![取得對話](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 根據所收集的 netmon 追蹤，我們可以告訴 TTL (TimeToLive) total 為64。 根據[本文](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)中所述的**預設 TTL 和躍點限制值** (如) 所述，我們可以看到它是重設套件的 Linux 系統，並會導致中斷連接。

    預設的 TTL 和躍點限制值會因不同的作業系統而異，以下是一些預設值：
    - Linux 核心 2.4 (約 2001) ： 255 for TCP、UDP 和 ICMP
    - Linux 核心 4.10 (2015) ： 64 for TCP、UDP 和 ICMP
    - 適用于 TCP、UDP 和 ICMP 的 Windows XP (2001) ：128
    - Windows 10 (2015) ：128（適用于 TCP、UDP 和 ICMP）
    - 適用于 TCP、UDP 和 ICMP 的 Windows Server 2008：128
    - Windows Server 2019 (2018) ： 128 for TCP、UDP 和 ICMP
    - macOS (2001) ：64（適用于 TCP、UDP 和 ICMP）

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    不過，在上述範例中，它會顯示為61而非64，因為當網路套件到達目的地時，它必須經過不同的躍點，例如路由器/網路裝置。 路由器/網路裝置數目將會在最終 TTL 中扣除。
    在此情況下，我們可以看到重設可能會從具有 TTL 64 的 Linux 系統傳送。

- 我們需要檢查來自自我裝載 IR 的第四個躍點，以確認重設裝置的來源。
 
    *來自 Linux 系統 A 且 TTL 為64的網路封裝-> B TTL 64 減 1 = 63-> C TTL 63 減去 1 = 62-> TTL 62 減 1 = 61 自我裝載 IR*

- 在理想的情況下，TTL 將會是128，這表示 Windows 系統正在執行 Data Factory。 如下列範例所示， *128 – 107 = 21 個躍點*，表示封裝的21個躍點在 TCP 3 信號交換期間，從 Data Factory 傳送到自我裝載的 IR。
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    因此，您必須與網路小組合作，檢查第四個躍點來自自我裝載 IR。 如果它是 Linux 系統的防火牆，則請檢查任何記錄檔，以瞭解裝置在 TCP 3 信號交換之後重設套件的原因。 但是，如果您不確定要在哪裡進行調查，請嘗試在有問題的時間內，從自我裝載的 IR 和防火牆取得 netmon 追蹤，以找出可能會重設此封裝並導致中斷連線的裝置。 在此情況下，您也需要與網路小組合作來繼續進行。

### <a name="how-to-analyze-netmon-trace"></a>如何分析 netmon 追蹤

> [!NOTE] 
> 以下指示適用于 netmon 追蹤。 由於 netmon 追蹤目前不支援，您可以利用 wireshark。

當您嘗試收集 netmon 追蹤的 telnet **8.8.8.8 888** 時，您應該會看到下列追蹤：

![netmon 追蹤1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon 追蹤2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

這表示您無法根據埠**888**對**8.8.8.8**伺服器端進行 TCP 連線，因此您會在該處看到兩個**SynReTransmit**額外的套件。 由於來源 **自我 host2.contoso.com** 無法連接到第一個封裝的 **8.8.8.8** ，因此會繼續進行連接。

> [!TIP]
> - 您可以按一下 [**載入篩選器**  ->  **標準篩選器**  ->  **定址**  ->  **IPv4 位址**]。
> - 輸入 **IPv4. Address = = 8.8.8.8** as filter， **然後按一下 [** 套用]。 之後，您將只會看到從本機電腦到目的地 **8.8.8.8**的通訊。

![篩選位址1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![篩選位址2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

以下範例顯示良好案例的外觀。 

- 如果 Telnet **8.8.8.8 53** 在沒有任何問題的情況下正常運作，您可以看到 tcp 3 交握，然後會話就會完成 tcp 4 交握。

    ![良好案例範例1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![良好案例範例2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 根據上述的 TCP 3 信號交換，您可以看到下列工作流程：

    ![TCP 3 交握工作流程](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 用來完成會話的 TCP 4 交握和其工作流程將顯示如下：

    ![TCP 4 交握](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 交握工作流程](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>接收電子郵件以更新網路設定，以允許與新的 IP 位址進行通訊

#### <a name="symptoms"></a>徵兆

您可能會收到下列電子郵件通知，建議您更新網路設定，以允許 Azure Data Factory 2020 年11月8日與新的 IP 位址進行通訊：

   ![電子郵件通知](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="resolution"></a>解決方法

此通知適用于從**內部部署**或**Azure 虛擬私人網路**中執行的**Integration Runtime**到 ADF 服務的**輸出通訊**。 例如，如果您在 Azure VNET 中有自我裝載的 IR 或 Azure SQL Server Integration Services (SSIS) IR，而這需要存取 ADF 服務，則您需要在網路安全性群組中新增此 IP 範圍， ** (NSG) ** 規則。 如果您的輸出 NSG 規則使用服務標籤，將不會有任何影響。

#### <a name="more-details"></a>其他詳細資訊

這些新的 IP 範圍 **只會影響** 來自 **內部部署防火牆** 或 **azure 虛擬私人網路** 的輸出通訊規則到 ADF 服務 (如需在內部部署網路或 azure 虛擬網路中有自我裝載 ir 或 SSIS ir 的情況下，您必須在內部部署網路或 azure 虛擬網路中使用自我裝載 ir 或 SSIS IR，) [而且](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 必須與 adf 服務通訊，

針對使用 **AZURE VPN**的現有使用者：

1. 檢查您私人網路中設定 SSIS 或 Azure SSIS 的任何輸出 NSG 規則。 如果沒有輸出限制，就不會影響它們。
1. 如果您有輸出規則限制，請檢查您是否使用服務標記。 如果您使用服務標籤，則不需要變更或新增任何新的 IP 範圍，因為新的 IP 範圍會在現有的服務標籤底下。 
  
    ![目的地檢查](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

1. 如果您直接在規則設定中使用 IP 位址，請檢查是否在服務標籤 [IP 範圍下載連結](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)中新增所有 ip 範圍。 我們已將新的 IP 範圍放在此檔案中。 針對新使用者：您只需要在檔中追蹤相關的自我裝載 IR 或 SSIS IR 設定，即可設定 NSG 規則。

適用于 **內部部署**具有 SSIS Ir 或自我裝載 ir 的現有使用者：

- 與您的網路基礎結構小組進行驗證，看看他們是否需要在輸出規則的通訊上包含新的 IP 範圍位址。
- 針對以 FQDN 名稱為基礎的防火牆規則，當您使用 [防火牆設定] 記錄的設定 [以及 ip 位址的允許清單設定](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway)時，不需要更新。 
- 某些內部部署防火牆支援服務標籤，如果您使用更新的 Azure 服務標籤設定檔，則不需要進行其他變更。

## <a name="self-hosted-ir-sharing"></a>自我裝載 IR 共用

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>不支援從不同的租使用者共用自我裝載 IR 

#### <a name="symptoms"></a>徵兆

您可能會注意到在不同的租使用者上 (其他資料處理站) 在嘗試從 Azure Data Factory UI 共用自我裝載的 IR，但無法在不同租使用者上的資料處理站之間共用自我裝載的 IR。

#### <a name="cause"></a>原因

自我裝載 IR 無法共用跨租使用者。


## <a name="next-steps"></a>後續步驟

如需疑難排解的詳細資訊，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [對應資料流程效能指南](concepts-data-flow-performance.md)
