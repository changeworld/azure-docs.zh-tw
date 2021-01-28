---
title: 針對在 Azure Data Factory 中的自我裝載整合執行階段進行疑難排解
description: 深入了解如何針對在 Azure Data Factory 中的自我裝載整合執行階段進行疑難排解。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 8a722550d12d019e25ff39de27cc0df2c2762a01
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942044"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>針對自我裝載整合執行階段進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中的自我裝載整合執行時間 (IR) 常見的疑難排解方法。

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>從 Azure Data Factory 收集自我裝載的 IR 記錄檔

針對在自我裝載 IR 或共用 IR 上執行的失敗活動，Azure Data Factory 支援查看和上傳錯誤記錄檔。 若要取得錯誤報表識別碼，請遵循此處的指示，然後輸入報表識別碼以搜尋相關的已知問題。

1. 在 Data Factory 中，選取 [ **管線執行**]。

1. 在 [ **活動執行**] 下的 [ **錯誤** ] 資料行中，選取反白顯示的按鈕以顯示活動記錄，如下列螢幕擷取畫面所示：

    ![[所有管線執行] 窗格上 [活動執行] 區段的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    活動記錄會針對失敗的活動執行顯示。

    ![失敗活動之活動記錄的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. 如需進一步的協助，請選取 [ **傳送記錄**]。
 
   會開啟 **與 Microsoft Window 共用的自我裝載整合執行時間 (IR) 記錄** 檔。

    ![[與 Microsoft 共用自我裝載整合執行時間 (IR) 記錄] 視窗的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 選取您要傳送的記錄檔。 
    * 若為 *自我裝載 ir*，您可以上傳與失敗活動相關的記錄，或自我裝載 ir 節點上的所有記錄。 
    * 針對 *共用 IR*，您只能上傳與失敗活動相關的記錄。

1. 上傳記錄時，如果您需要進一步協助來解決問題，請保留報表識別碼的記錄以供稍後使用。

    ![螢幕擷取畫面： IR 記錄的 [上傳進度] 視窗中顯示的報告識別碼。](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 記錄檔的查看和上傳要求是在所有線上自我裝載 IR 實例上執行。 如果有任何記錄檔遺失，請確定所有自我裝載的 IR 實例都在線上。 


## <a name="self-hosted-ir-general-failure-or-error"></a>自我裝載 IR 常見失敗或錯誤

### <a name="out-of-memory-issue"></a>記憶體不足問題

#### <a name="symptoms"></a>徵兆

當您嘗試使用連結的 IR 或自我裝載 IR 來執行查閱活動時，會發生 OutOfMemoryException (OOM) 錯誤。

#### <a name="cause"></a>原因

如果 IR 機器遇到過高的記憶體使用量，新的活動可能會擲回 OOM 錯誤。 問題可能是由大量並行活動所造成，而錯誤則是設計所致。

#### <a name="resolution"></a>解決方案

檢查 IR 節點上的資源使用方式和並行活動執行。 調整活動執行的內部和觸發時間，以避免同時在單一 IR 節點上執行太多的作業。

### <a name="concurrent-jobs-limit-issue"></a>並行作業數限制問題

#### <a name="symptoms"></a>徵兆

當您嘗試從 Azure Data Factory 介面增加並行作業限制時，進程會在 [ *更新* 狀態] 中停止回應。

範例案例：並行作業的最大值目前設定為24，而您想要增加計數，讓您的工作可以更快執行。 您可以輸入的最小值為3，而最大值為32。 您可以將值從24增加到32，然後選取 [ **更新** ] 按鈕。 進程會停滯于 *更新* 狀態，如下列螢幕擷取畫面所示。 您會重新整理頁面，而該值仍會顯示為24。 它未如預期般更新為32。

![整合執行時間的 [節點] 窗格螢幕擷取畫面，顯示程式卡在「正在更新」狀態。](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>原因

並行作業數目的限制取決於電腦的邏輯核心和記憶體。 請嘗試將值向下調整至值（例如24），然後再查看結果。

> [!TIP] 
> - 若要深入瞭解邏輯核心計數，並判斷您電腦的邏輯核心計數，請參閱 [四種在 Windows 10 上找出 CPU 中核心數目的方式](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)。
> - 若要瞭解如何計算 math，請移至 [對數計算機](https://www.rapidtables.com/calc/math/Log_Calculator.html)。


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>自我裝載 IR 高可用性 (HA) SSL 憑證問題

#### <a name="symptoms"></a>徵兆

自我裝載 IR 工作節點已回報下列錯誤：

「無法從主要節點 net.tcp：//abc.cloud.corp.Microsoft.com 提取共用狀態： 8060/ExternalService .svc/。 活動識別碼： XXXXX x.509 certificate CN = abc.. a X. .com，OU = test，O = Microsoft chain 建立失敗。 使用的憑證擁有無法驗證的信賴鏈結。 請取代憑證或變更 certificateValidationMode。 撤銷功能無法檢查撤銷，因為撤銷伺服器已離線。」

#### <a name="cause"></a>原因

當您處理與 SSL/TLS 交握相關的案例時，您可能會遇到一些與憑證鏈驗證相關的問題。 

#### <a name="resolution"></a>解決方案

- 以下是針對 x.509 憑證鏈組建失敗進行疑難排解的快速、直覺方式：
 
    1. 匯出需要驗證的憑證。 若要這樣做，請執行下列動作：
    
       a. 在 Windows 中，選取 [ **開始**]，開始輸入 **憑證**，然後選取 [ **管理電腦憑證**]。
       
       b. 在檔案總管的左窗格中，搜尋您要檢查的憑證、以滑鼠右鍵按一下該憑證，然後選取 [所有工作  >  **匯出**]。
    
        ![[管理電腦憑證] 窗格上之憑證的 [所有工作] > [匯出] 控制項的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. 將匯出的憑證複製到用戶端電腦。 
    3. 在用戶端的命令提示字元視窗中，執行下列命令。 請務必將和取代為 *\<certificate path>* *\<output txt file path>* 實際的路徑。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        例如：

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 檢查輸出 TXT 檔案中的錯誤。 您可以在 TXT 檔案的結尾找到錯誤摘要。

        例如： 

        ![TXT 檔案結尾的錯誤摘要螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        如果您在記錄檔的結尾沒有看到錯誤，如下列螢幕擷取畫面所示，您可以考慮在用戶端電腦上成功建立憑證鏈。
        
        ![顯示沒有錯誤之記錄檔的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- 如果 AIA (授權單位資訊存取) 、CDP (CRL 發佈點) 或 OCSP (線上憑證狀態通訊協定) 副檔名是在憑證檔案中設定，您可以用更直覺的方式來檢查它：
 
    1. 檢查憑證詳細資料以取得這項資訊，如下列螢幕擷取畫面所示：
    
        ![憑證詳細資料的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. 執行下列命令。 請務必將取代為 *\<certificate path>* 憑證的實際路徑。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        [URL 抓取] 工具隨即開啟。 
        
    1. 若要使用 AIA、CDP 和 OCSP 副檔名來確認憑證，請選取 [ **取出**]。

        ![URL 抓取工具和 [抓取] 按鈕的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        如果已 *驗證* AIA 的憑證狀態，且 *已驗證* CDP 或 OCSP 的憑證狀態，則您已成功建立憑證鏈。

        如果您嘗試抓取 AIA 或 CDP 時失敗，請與您的網路小組合作，讓用戶端電腦可以連線到目標 URL。 如果可以驗證 HTTP 路徑或輕量型目錄存取協定 (LDAP) 路徑，這將會足夠。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>自我裝載 IR 無法載入檔案或組件

#### <a name="symptoms"></a>徵兆

您會收到下列錯誤訊息：

「無法載入檔案或元件 ' XXXXXXXXXXXXXXXX，Version = 4.0.2.0，Culture = 中性，PublicKeyToken = XXXXXXXXX ' 或其相依性的其中之一。 系統找不到指定的檔案。 活動識別碼： 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "
 
以下是更具體的錯誤訊息： 

「無法載入檔案或元件 ' ValueTuple，Version = 4.0.2.0，Culture = 中性，PublicKeyToken = XXXXXXXXX ' 或其相依性的其中之一。 系統找不到指定的檔案。 活動識別碼： 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>原因

在進程監視器中，您可以看到下列結果：

[![進程監視器中 [路徑] 清單的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 在 [進程監視器] 中，您可以設定篩選器，如下列螢幕擷取畫面所示。
>
> 上述錯誤訊息指出 DLL ValueTuple 不在相關的 *全域組件快取* (GAC) 資料夾、 *C:\Program Files\Microsoft integration Runtime\4.0\Gateway* 資料夾或 *C:\Program Files\Microsoft Integration Runtime\4.0\Shared* 資料夾中。
>
> 基本上，此程式會先從 *GAC* 資料夾載入 DLL，然後從 *共用* 資料夾載入，最後從 *Gateway* 資料夾載入 DLL。 因此，您可以從任何有用的路徑載入 DLL。

<br>

![[進程監視器篩選] 頁面的螢幕擷取畫面，其中列出 DLL 的篩選。](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>解決方案

您會在 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* 資料夾中找到 *System.ValueTuple.dll* 檔案。 若要解決此問題，請將 *System.ValueTuple.dll* 檔案複製到 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 資料夾。

您可以使用相同的方法來解決其他遺失的檔案或元件問題。

#### <a name="more-information-about-this-issue"></a>有關此問題的詳細資訊

您在 *%windir%\Microsoft.NET\assembly* 和 *%windir%\assembly* 下看到 *System.ValueTuple.dll* 的原因是這是 .net 的行為。 

在下列錯誤中，您可以清楚地看到 *ValueTuple* 元件遺失。 當應用程式嘗試檢查 *System.ValueTuple.dll* 元件時，就會發生這個問題。
 
" \<LogProperties> \<ErrorInfo> [{" Code "：0，" Message "：" ' Npgsql. PoolManager ' 的類型初始化運算式擲回例外狀況。 "，" "：0，" Category "：5，" Data "： {} ，" MsgId "： Null，" ExceptionType "：" system.typeinitializationexception "，" Source "：" Npgsql "，" StackTrace "：" "，" InnerEventInfos "： [{" Code "：0，" Message "：" 無法載入檔案或元件 ' System. ValueTuple，Version = 4.0.2.0，Culture = 中性，PublicKeyToken = XXXXXXXXX ' 或其相依性的其中之一。 系統找不到指定的檔案。 "，":null "：0，" Category "：5，" Data "： {} ，" MsgId"，"ExceptionType":"System，FileNotFoundException "，" Source "：" Npgsql "，" StackTrace "：" "，" InnerEventInfos "： []}]}] \</ErrorInfo> \</LogProperties> "
 
如需 GAC 的詳細資訊，請參閱 [全域組件快取](/dotnet/framework/app-domains/gac)。


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>缺少自我裝載整合執行時間驗證金鑰

#### <a name="symptoms"></a>徵兆

自我裝載整合執行時間會在沒有驗證金鑰的情況下突然離線，而事件記錄檔會顯示下列錯誤訊息： 

「尚未指派驗證金鑰」

![[Integration runtime] 事件窗格的螢幕擷取畫面，顯示尚未指派驗證金鑰。](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>原因

- 已刪除 Azure 入口網站中的自我裝載 IR 節點或邏輯自我裝載 IR。
- 執行了全新卸載。

#### <a name="resolution"></a>解決方案

如果上述原因都不適用，您可以移至 [ *%Programdata%\Microsoft\Data Transfer\DataManagementGateway* ] 資料夾，查看 *配置* 檔是否已刪除。 如果已刪除，請依照 Netwrix 文章中的指示，偵測 [從 Windows 檔案伺服器刪除](https://www.netwrix.com/how_to_detect_who_deleted_file.html)檔案的人員。

![檢查設定檔案的 [事件記錄檔詳細資料] 窗格螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>無法使用自我裝載 IR 來橋接兩個內部部署資料存放區

#### <a name="symptoms"></a>徵兆

在您為來源和目的地資料存放區建立自我裝載的 IRs 之後，您會想要連接兩個 IRs 來完成複製活動。 如果資料存放區是在不同的虛擬網路中設定，或資料存放區無法瞭解閘道機制，您會收到下列其中一個錯誤： 

* 「在目的地 IR 中找不到來源的驅動程式」
* 「目的地 IR 無法存取來源」
 
#### <a name="cause"></a>原因

自我裝載 IR 是設計為複製活動的中央節點，而不是需要針對每個資料存放區安裝的用戶端代理程式。
 
在此情況下，您應該為具有相同 IR 的每個資料存放區建立連結的服務，而 IR 應該能夠透過網路存取這兩個數據存放區。 無論 IR 是安裝在來源資料存放區或目的地資料存放區，還是在第三台電腦上。 如果有兩個連結的服務是使用不同的 IR 所建立，但在相同的複製活動中使用，則會使用目的地 IR，而您必須在目的地 IR 機器上安裝這兩個數據存放區的驅動程式。

#### <a name="resolution"></a>解決方案

針對目的地 IR 上的來源和目的地資料存放區安裝驅動程式，並確定它可以存取來源資料存放區。
 
如果流量無法在兩個數據存放區之間通過網路 (例如，它們是在兩個虛擬網路) 中設定的，您可能無法在一個活動中完成複製，即使已安裝 IR 也是一樣。 如果您無法在單一活動中完成複製作業，您可以建立兩個具有兩個 IRs 的複製活動，每個都在通風中： 
* 從資料存放區1將一個 IR 複製到 Azure Blob 儲存體
* 將另一個 IR 從 Azure Blob 儲存體複製到 ddatastore 2。 

此解決方案可以模擬使用 IR 來建立連接兩個中斷連線資料存放區的橋接器的需求。


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>認證同步問題導致 HA 遺失認證

#### <a name="symptoms"></a>徵兆

如果從目前的 integration runtime 節點刪除資料來源認證 "XXXXXXXXXX" 與承載，您會收到下列錯誤訊息：

「當您在 Azure 入口網站上刪除連結服務，或工作具有錯誤的承載時，請再次使用您的認證建立新的連結服務。」

#### <a name="cause"></a>原因

您的自我裝載 IR 建置於具有兩個節點的 HA 模式中，但節點未處於認證同步狀態。 這表示儲存在發送器節點中的認證不會同步處理至其他背景工作節點。 如果從發送器節點到背景工作節點發生任何容錯移轉，而且認證只存在於先前的發送器節點中，則當您嘗試存取認證時，工作將會失敗，而且您會收到上述錯誤。

#### <a name="resolution"></a>解決方案

避免這個問題的唯一方法，是確定兩個節點都處於認證同步狀態。 如果它們不同步，您必須重新輸入新發送器的認證。


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>因為遺失私密金鑰，所以無法選擇憑證

#### <a name="symptoms"></a>徵兆

* 您已將 PFX 檔案匯入至憑證存放區。
* 當您透過 IR 設定管理員 UI 選取憑證時，您收到下列錯誤訊息：

   「無法變更內部網路通訊加密模式。 可能是憑證 ' \<*certificate name*> ' 可能沒有能夠進行金鑰交換的私密金鑰，或該處理常式可能沒有私密金鑰的存取權限。 請參閱內部例外狀況以取得詳細資料。」

    ![Integration Runtime 設定管理員設定] 窗格的螢幕擷取畫面，其中顯示「遺失私密金鑰」錯誤訊息。](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>原因

- 使用者帳戶具有低許可權層級，而且無法存取私密金鑰。
- 憑證是以簽章的形式產生，而不是以金鑰交換形式產生。

#### <a name="resolution"></a>解決方案

* 若要操作 UI，請使用具有適當許可權的帳戶來存取私密金鑰。  
* 藉由執行下列命令來匯入憑證：
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>自我裝載 IR 設定

### <a name="integration-runtime-registration-error"></a>整合執行時間註冊錯誤 

#### <a name="symptoms"></a>徵兆

基於下列其中一個原因，您可能偶爾會想要在不同的帳戶中執行自我裝載的 IR：
- 公司原則不允許服務帳戶。
- 需要進行某些驗證。

在 [服務] 窗格上變更服務帳戶之後，您可能會發現整合執行時間停止運作，而且您會收到下列錯誤訊息：

「Integration Runtime (自我裝載) 節點在註冊期間發生錯誤。 無法連接至 Integration Runtime (自我裝載) 主機服務」。

![顯示 IR 註冊錯誤 Integration Runtime 設定管理員視窗的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>原因

許多資源只會授與服務帳戶。 當您將服務帳戶變更為另一個帳戶時，所有相依資源的許可權都會保持不變。

#### <a name="resolution"></a>解決方案

移至 integration runtime 事件記錄檔來檢查錯誤。

![IR 事件記錄檔的螢幕擷取畫面，顯示發生執行階段錯誤。](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* 如果事件記錄檔中的錯誤是 "System.unauthorizedaccessexception"，請執行下列動作：

    1. 檢查 Windows 服務面板中的 *DIAHostService* logon 服務帳戶。

        ![[登入服務帳戶屬性] 窗格的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. 查看登入服務帳戶是否具有 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* 資料夾的讀取/寫入權限。

        - 根據預設，如果服務登入帳戶尚未變更，它應該有讀取/寫入權限。

            ![[服務許可權] 窗格的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - 如果您已變更服務登入帳戶，請執行下列動作來緩解此問題：
 
            a. 執行目前自我裝載 IR 的全新卸載。   
            b. 安裝自我裝載 IR 位。  
            c. 執行下列動作來變更服務帳戶：  

             i. 移至自我裝載的 IR 安裝資料夾，然後切換至 *Microsoft Integration Runtime\4.0\Shared* 資料夾。  
             ii. 使用較高的許可權開啟 [命令提示字元] 視窗。 將 *\<user>* 和取代 *\<password>* 為您自己的使用者名稱和密碼，然後執行下列命令：   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. 如果您想要變更為 LocalSystem 帳戶，請務必針對此帳戶使用正確的格式： `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                請勿 *使用此* 格式： `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. （選擇性）由於本機系統的許可權高於系統管理員，您也可以在 [服務] 中直接變更。  
             v. 您可以使用 IR 服務登入帳戶的本機/網域使用者。            

            d. 註冊整合執行時間。

* 如果錯誤為「服務」 Integration Runtime 服務 ' (DIAHostService) 無法啟動。 確認您有足夠的許可權可以啟動系統服務」，請執行下列動作：

    1. 檢查 Windows 服務面板中的 *DIAHostService* logon 服務帳戶。
    
        ![服務帳戶的 [登入] 窗格螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. 請檢查登入服務帳戶是否具有啟動 Windows 服務的「 **以服務方式登** 入」許可權：

        ![[以服務方式登入] 屬性窗格的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>詳細資訊

如果上述兩個解析模式都未套用在您的案例中，請嘗試收集下列 Windows 事件記錄檔： 
- 應用程式和服務記錄 > Integration Runtime
- Windows 記錄 > 應用程式

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>找不到註冊按鈕以註冊自我裝載 IR    

#### <a name="symptoms"></a>徵兆

當您註冊自我裝載 IR 時，[暫存器] 按鈕不會顯示 **在 [設定管理員** ] 窗格中。

![設定管理員窗格的螢幕擷取畫面，顯示未註冊 integration runtime 節點的訊息。](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>原因

從 Integration Runtime 3.0 的版本中，已移除現有 Integration Runtime 節點上的 [ **註冊** ] 按鈕，以啟用更清楚且更安全的環境。 如果節點已註冊至整合執行時間（無論是否在線上），請卸載前一個節點將其註冊至另一個整合執行時間，然後再安裝並註冊節點。

#### <a name="resolution"></a>解決方案

1. 在主控台中，卸載現有的整合執行時間。

    > [!IMPORTANT] 
    > 在下列流程中，選取 **[是]**。 請勿在卸載過程中保留資料。

    ![[是] 按鈕的螢幕擷取畫面，可刪除整合執行時間中的所有使用者資料。](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. 如果您沒有整合執行時間安裝程式 MSI 檔案，請前往 [下載中心](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) 下載最新的整合執行時間。
1. 安裝 MSI 檔案，並註冊整合執行時間。


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>因為 localhost，所以無法註冊自我裝載 IR    

#### <a name="symptoms"></a>徵兆

當您使用 get_LoopbackIpOrName 時，無法在新的電腦上註冊自我裝載 IR。

**Debug：** 發生執行階段錯誤。
'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' 的類型初始設定式擲回例外狀況。
資料庫查閱期間發生無法復原的錯誤。
 
**例外狀況詳細資料：** System.typeinitializationexception： ' Microsoft. DataTransfer. DIAgentHost. DataSourceCache ' 的類型初始化運算式擲回例外狀況。 ---> >socketexception：在系統 GetAddrInfo 的資料庫查閱期間，發生無法復原的錯誤。 (字串名稱) 。

#### <a name="cause"></a>原因

解決 localhost 時，通常就會發生此問題。

#### <a name="resolution"></a>解決方案

使用 localhost IP 位址127.0.0.1 來裝載檔案並解決問題。

### <a name="self-hosted-setup-failed"></a>自我裝載設定失敗    

#### <a name="symptoms"></a>徵兆

您無法卸載現有的 IR、安裝新的 IR，或將現有的 IR 升級至新的 IR。

#### <a name="cause"></a>原因

Integration runtime 安裝取決於 Windows Installer 服務。 基於下列原因，您可能會遇到安裝問題：
- 可用的磁碟空間不足。
- 缺少許可權。
- Windows NT 服務已鎖定。
- CPU 使用率過高。
- MSI 檔案裝載于較慢的網路位置。
- 某些系統檔案或登錄不慎觸及。

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>IR 服務帳戶無法提取憑證存取權

#### <a name="symptoms"></a>徵兆

當您透過 Microsoft Integration Runtime 設定管理員安裝自我裝載 IR 時，會產生具有信任憑證授權單位單位的憑證 (CA) 。 無法套用憑證來加密兩個節點之間的通訊，並顯示下列錯誤訊息： 

「無法變更內部網路通訊加密模式：無法授與 Integration Runtime 服務帳戶對憑證 ' ' 的存取權 \<*certificate name*> 。 錯誤碼103」

![顯示錯誤訊息的螢幕擷取畫面 ".。。無法授與 Integration Runtime 服務帳戶憑證存取權」。](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>原因

憑證正在使用金鑰儲存提供者 (KSP) 儲存體，但目前尚不支援此功能。 至目前為止，自我裝載 IR 僅支援密碼編譯服務提供者 (CSP) 儲存體。

#### <a name="resolution"></a>解決方案

建議您在此情況下使用 CSP 憑證。

**解決方案 1** 

若要匯入憑證，請執行下列命令：

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![匯入憑證的 certutil 命令螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**解決方案 2** 

若要轉換憑證，請執行下列命令：

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

轉換前後：

![憑證轉換之前的結果螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![憑證轉換之後的結果螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>自我裝載整合執行階段版本5。x
若要升級至 Azure Data Factory 自我裝載整合執行時間的5.x 版，我們需要 **.NET Framework runtime 4.7.2** 或更新版本。 在下載頁面上，您可以找到最新的4.x 版本和最新兩個5.x 版本的下載連結。 

針對 Azure Data Factory v2 客戶：
- 如果已開啟自動更新，且您已將 .NET Framework 執行時間升級至4.7.2 或更新版本，自我裝載整合執行時間會自動升級為最新的5.x 版。
- 如果自動更新是開啟的，而且您尚未將 .NET Framework 執行時間升級至4.7.2 或更新版本，自我裝載整合執行時間將不會自動升級為最新的5.x 版。 自我裝載整合執行時間會保持在目前的4.x 版本。 您可以在入口網站和自我裝載整合執行時間用戶端查看 .NET Framework 執行時間升級的警告。
- 如果自動更新已關閉，且您已將 .NET Framework 執行時間升級至4.7.2 或更新版本，您可以手動下載最新的5.x，並將它安裝在您的電腦上。
- 如果自動更新已關閉，而且您尚未將 .NET Framework 執行時間升級至4.7.2 或更新版本。 當您嘗試手動安裝自我裝載整合執行時間5.x 並註冊金鑰時，您必須先升級 .NET Framework 執行階段版本。


針對 Azure Data Factory v1 客戶：
- 自我裝載整合執行時間5.x 不支援 Azure Data Factory v1。
- 自我裝載整合執行時間會自動升級為最新版本的4.x。 最新版本的4.x 將不會過期。 
- 如果您嘗試手動安裝自我裝載整合執行時間5.x 並註冊金鑰，將會通知您自我裝載 integration runtime 5.x 不支援 Azure Data Factory v1。


## <a name="self-hosted-ir-connectivity-issues"></a>自我裝載 IR 連接問題

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>自我裝載整合執行時間無法連接到雲端服務

#### <a name="symptoms"></a>徵兆

當您嘗試註冊自我裝載整合執行時間時，設定管理員會顯示下列錯誤訊息：

「Integration Runtime (自我裝載的) 節點在註冊期間發生錯誤」。

![「Integration Runtime (自我裝載的) 節點在註冊期間發生錯誤」訊息的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自我裝載 IR 無法連線到 Azure Data Factory service 後端。 此問題通常是因為防火牆中的網路設定所造成。

#### <a name="resolution"></a>解決方案

1. 檢查以查看 integration runtime 服務是否正在執行。 如果是，請移至步驟2。
    
   ![螢幕擷取畫面，顯示自我裝載 IR 服務正在執行。](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果自我裝載 IR （這是預設設定）上未設定 proxy，請在安裝自我裝載整合執行時間的電腦上執行下列 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 根據您的 data factory 實例的位置而定，服務 URL 可能會有所不同。 若要尋找服務 URL，請選取 [ **ADF UI**  >  **連接**  >  **整合運行** 時間]  >  **編輯自我裝載的 IR**  >  **節點**  >  **View service url**。
            
    預期的回應如下：
            
    ![PowerShell 命令回應的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 如果您沒有收到預期的回應，請視需要使用下列其中一種方法：
            
    * 如果您收到「無法解析遠端名稱」訊息，即表示有網域名稱系統 (DNS) 問題。 請洽詢您的網路小組以修正此問題。
    * 如果您收到「不受信任的 ssl/tls 憑證」訊息，請 [檢查憑證](https://wu2.frontend.clouddatahub.net/) 以查看電腦上是否受信任，然後使用 [憑證管理員] 安裝公開憑證。 這個動作應可減輕此問題。
    * 移至 **Windows**  >  **事件檢視器 (記錄)**  >  **應用程式和服務記錄** 檔  >  **Integration Runtime**，並檢查 DNS、防火牆規則或公司網路設定所造成的任何失敗。 如果您發現此類失敗，請強制關閉連接。 因為每一家公司都有自己的自訂網路設定，請洽詢您的網路小組以針對這些問題進行疑難排解。

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
            
![預期 Powershell 命令回應的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy 考量：
> * 查看是否需要將 proxy 伺服器放在安全收件者清單中。 若是如此，請確定[這些網域](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) 列於安全收件者的清單上。
> * 查看 proxy 伺服器上的 SSL/TLS 憑證 "wu2.frontend.clouddatahub.net/" 是否受信任。
> * 如果您在 Proxy 上使用 Active Directory 驗證，請將服務帳戶變更為可將 Proxy 作為「整合執行階段服務」存取的使用者帳戶。

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>錯誤訊息：自我裝載 integration runtime 節點/邏輯自我裝載 IR 處於非使用中/「正在執行 (有限) 」狀態

#### <a name="cause"></a>原因 

自我裝載整合執行時間節點的狀態可能為 [ **非** 作用中]，如下列螢幕擷取畫面所示：

![處於非作用中狀態的自我裝載整合執行時間節點螢幕擷取畫面](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

當節點無法彼此通訊時，即會發生這種行為。

#### <a name="resolution"></a>解決方案

1. 登入節點託管的虛擬機器 (VM) 。 在 [**應用程式和服務記錄** 檔  >  **Integration Runtime**] 下，開啟事件檢視器，然後篩選錯誤記錄檔。

1. 查看錯誤記錄檔是否包含下列錯誤： 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 如果您看到此錯誤，請在命令提示字元視窗中執行下列命令： 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 如果您收到下列螢幕擷取畫面所示的「無法開啟主機的連線」命令列錯誤，請洽詢您的 IT 部門以取得協助以修正此問題。 順利完成 telnet 之後，如果您仍有整合執行時間節點狀態的問題，請聯絡 Microsoft 支援服務。
        
   ![[無法開啟主機連線] 命令列錯誤的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 查看錯誤記錄檔是否包含下列專案：

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 若要解決此問題，請嘗試下列其中一種或兩種方法：
    - 將所有節點放在相同的網域中。
    - 在所有託管 VM 的主機檔案中新增 IP 至主機的對應。

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>自我裝載 IR 和您的 data factory 實例或自我裝載 IR 與資料來源或接收之間的連線問題

若要疑難排解網路連線問題，您應該先瞭解如何收集網路追蹤、瞭解如何使用它，以及 [分析 Microsoft 網路監視器 (Netmon) 追蹤](#analyze-the-netmon-trace) ，然後再從自我裝載的 IR 在實際情況下套用 Netmon 工具。

#### <a name="symptoms"></a>徵兆

您可能偶爾需要針對自我裝載 IR 與 data factory 實例之間的特定連線問題進行疑難排解（如下列螢幕擷取畫面所示），或在自我裝載 IR 與資料來源或接收之間進行疑難排解。 

![[已處理的 HTTP 要求失敗] 訊息的螢幕擷取畫面](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

在任一種情況下，您可能會遇到下列錯誤：

* 「複製失敗，錯誤： Type = DataTransfer。 HybridDeliveryException，Message = 無法連線到 SQL Server： ' IP address '」

* 「發生一或多個錯誤。 傳送要求時發生錯誤。 基礎連接已關閉：接收時發生未預期的錯誤。 無法從傳輸連線讀取資料：遠端主機已強制關閉現有的連接。 遠端主機活動識別碼已強制關閉現有的連接。」

#### <a name="resolution"></a>解決方案

當您遇到上述錯誤時，請遵循本節中的指示來進行疑難排解。

- 收集用於分析的 Netmon 追蹤： 

    1. 您可以設定篩選器，以查看從伺服器到用戶端的重設。 在下列範例螢幕擷取畫面中，您可以看到伺服器端是 Data Factory server。

        ![Data factory 伺服器的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. 當您取得重設套件時，您可以遵循 (TCP) 的傳輸控制通訊協定來尋找交談。

        ![TCP 交談的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. 藉由移除篩選器來取得用戶端與 Data Factory 伺服器之間的交談。

        ![對話詳細資料的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- 您已收集的 Netmon 追蹤分析顯示 (TTL) # A2 總計的存留時間為64。 根據 [ [IP 存留時間 (TTL]) 和 [躍點限制基本](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) ] 文章中所述的值，在下列清單中解壓縮，您可以看到它是重設套件並導致中斷連接的 Linux 系統。

    預設的 TTL 和躍點限制值會因不同的作業系統而有所不同，如下所示：
    - Linux 核心 2.4 (約 2001) ： 255 for TCP、使用者資料包協定 (UDP) ，以及網際網路控制訊息通訊協定 (ICMP) 
    - Linux 核心 4.10 (2015) ： 64 for TCP、UDP 和 ICMP
    - 適用于 TCP、UDP 和 ICMP 的 Windows XP (2001) ：128
    - Windows 10 (2015) ：128（適用于 TCP、UDP 和 ICMP）
    - 適用于 TCP、UDP 和 ICMP 的 Windows Server 2008：128
    - Windows Server 2019 (2018) ： 128 for TCP、UDP 和 ICMP
    - macOS (2001) ：64（適用于 TCP、UDP 和 ICMP）

    ![顯示 TTL 值61的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    在上述範例中，TTL 會顯示為61而非64，因為當網路套件到達其目的地時，它需要經過各種躍點，例如路由器或網路裝置。 會扣除路由器或網路裝置的數目，以產生最終的 TTL。
    
    在此情況下，您可以看到可從 Linux 系統使用 TTL 64 傳送重設。

-  若要確認重設裝置的來源，請檢查來自自我裝載 IR 的第四個躍點。
 
    *來自 Linux 系統 A 且 TTL 為64的網路封裝-> B TTL 64 減 1 = 63-> C TTL 63 減去 1 = 62-> TTL 62 減 1 = 61 自我裝載 IR*

- 在理想的情況下，TTL 躍點號碼會是128，這表示 Windows 作業系統正在執行您的 data factory 實例。 如下列範例所示， *128 減去 107 = 21 個躍點*，這表示封裝的21個躍點是在 TCP 3 信號交換期間，從 data factory 實例傳送到自我裝載的 IR。
 
    ![顯示 TTL 值107的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    因此，您需要與網路小組合作，以查看第四個躍點來自自我裝載的 IR。 如果是防火牆（如同 Linux 系統），請檢查任何記錄檔，以瞭解該裝置在 TCP 3 信號交換之後重設套件的原因。 
    
    如果您不確定要調查的位置，請嘗試在有問題的時間內從自我裝載的 IR 和防火牆取得 Netmon 追蹤。 這種方法可協助您找出哪些裝置可能已重設套件，並導致中斷連接。 在此情況下，您也需要與網路小組合作來繼續進行。

### <a name="analyze-the-netmon-trace"></a>分析 Netmon 追蹤

> [!NOTE] 
> 下列指示適用于 Netmon 追蹤。 由於 Netmon 追蹤目前不支援，因此您可以使用 Wireshark 來進行此用途。

當您使用所收集的 Netmon 追蹤嘗試 telnet **8.8.8.8 888** 時，您應該會在下列螢幕擷取畫面中看到追蹤：

![顯示「無法開啟埠888上的主機連線」錯誤訊息的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![顯示 Netmon 追蹤描述的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

上述映射顯示您無法在埠 **888** 上建立與 **8.8.8.8** 伺服器端的 TCP 連線，因此您會在該處看到兩個 **SynReTransmit** 額外的套件。 由於來源 **自我 host2.contoso.com** 無法連接到具有第一個封裝的 **8.8.8.8** ，因此會繼續嘗試建立連接。

> [!TIP]
> 若要建立此連接，請嘗試下列解決方案：
> 1. 選取 [**載入篩選器**  >  **標準篩選器**  >  **定址**  >  **IPv4 位址**]。
> 1. 若要套用篩選，請輸入 **IPv4. Address = = 8.8.8.8**， **然後選取 [** 套用]。 接著，您應該會看到從本機電腦到目的地 **8.8.8.8** 的通訊。

![顯示篩選器位址的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![顯示更多篩選器位址的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

成功的案例如下列範例所示： 

- 如果您可以使用 telnet **8.8.8.8 53** 而沒有任何問題，則會成功進行 tcp 3 信號交換，且會話會完成 tcp 4 信號交換。

    ![顯示成功連接案例的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![顯示成功連接案例詳細資料的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 先前的 TCP 3 信號交換會產生下列工作流程：

    ![TCP 3 交握工作流程的圖表。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 下列工作流程說明完成會話的 TCP 4 交握：

    ![TCP 4 交握詳細資料的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 交握工作流程的圖表。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>關於更新網路設定的 Microsoft 電子郵件通知

您可能會收到下列電子郵件通知，建議您更新網路設定，以允許在2020年11月8日的 Azure Data Factory 與新的 IP 位址進行通訊：

   ![Microsoft 電子郵件通知要求更新網路設定的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>判斷此通知是否會影響您

此通知適用于下列案例：

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>案例1：從在公司防火牆後方執行內部部署的自我裝載整合執行時間輸出通訊

如何判斷您是否受影響：

- 如果您定義的防火牆規則是以完整功能變數名稱為基礎 (Fqdn) 使用 [[設定防火牆設定和允許的 IP 位址清單](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway)] 中所述的方法，則 *不會* 受到影響。

- 如果您在公司防火牆上明確啟用輸出 Ip 的允許清單，則 *會* 受到影響。

   如果您受到影響，請採取下列動作：在2020年11月8日，通知您的網路基礎結構小組更新您的網路設定，以使用最新的 data factory IP 位址。 若要下載最新的 IP 位址，請移至 [使用可下載的 JSON 檔案探索服務標記](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>案例2：從客戶管理的 Azure 虛擬網路內的 Azure VM 上執行的自我裝載整合執行時間輸出通訊

如何判斷您是否受影響：

- 查看是否有任何輸出網路安全性群組 (在包含自我裝載整合執行時間的私人網路中 NSG) 規則。 如果沒有輸出限制，就不會受到影響。

- 如果您有輸出規則限制，請檢查您是否使用服務標記。 如果您使用的是服務標籤，則不會受到影響。 因為新的 IP 範圍位於您現有的服務標籤底下，所以不需要變更或新增任何功能。 

  ![顯示 DataFactory 做為目的地之目的地檢查的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- 如果您要在 Azure 虛擬網路上的 NSG 規則設定上明確啟用輸出 IP 位址的允許清單，則 *會* 受到影響。

   如果您受到影響，請採取下列動作：在2020年11月8日，通知您的網路基礎結構小組更新 Azure 虛擬網路設定上的 NSG 規則，以使用最新的 data factory IP 位址。 若要下載最新的 IP 位址，請移至 [使用可下載的 JSON 檔案探索服務標記](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>案例3：從客戶管理的 Azure 虛擬網路中，SSIS Integration Runtime 的輸出通訊

如何判斷您是否受影響：

- 檢查是否有在私人網路中包含 SQL Server Integration Services (SSIS) Integration Runtime 的任何輸出 NSG 規則。 如果沒有輸出限制，就不會受到影響。

- 如果您有輸出規則限制，請檢查您是否使用服務標記。 如果您使用的是服務標籤，則不會受到影響。 因為新的 IP 範圍位於您現有的服務標籤底下，所以不需要變更或新增任何部分。

- 如果您要在 Azure 虛擬網路上的 NSG 規則設定上明確啟用輸出 IP 位址的允許清單，則 *會* 受到影響。

  如果您受到影響，請採取下列動作：在2020年11月8日，通知您的網路基礎結構小組更新 Azure 虛擬網路設定上的 NSG 規則，以使用最新的 data factory IP 位址。 若要下載最新的 IP 位址，請移至 [使用可下載的 JSON 檔案探索服務標記](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>無法建立 SSL/TLS 安全通道的信任關係 

#### <a name="symptoms"></a>徵兆

自我裝載 IR 無法連接到 Azure Data Factory 服務。

當您在 CustomLogEvent 資料表中檢查自我裝載的 IR 事件記錄檔或用戶端通知記錄時，將會看到下列錯誤訊息：

「基礎連接已關閉：無法為 SSL/TLS 安全通道建立信任關係。 根據驗證程序，遠端憑證無效。」

若要檢查 Data Factory 服務的伺服器憑證，最簡單的方式就是在瀏覽器中開啟 Data Factory 服務 URL。 例如，在已安裝自我裝載 IR 的電腦上開啟 [ [檢查伺服器憑證] 連結](https://eu.frontend.clouddatahub.net/) ，然後查看伺服器憑證資訊。

  ![Azure Data Factory 服務的 [檢查伺服器憑證] 窗格螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![檢查伺服器憑證路徑之視窗的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>原因

這個問題有兩個可能的原因：

- 原因1：在安裝自我裝載 IR 的電腦上，不信任 Data Factory service server 憑證的根 CA。 
- 原因2：您使用的是環境中的 proxy，Data Factory 服務的伺服器憑證會被 proxy 取代，而已取代的伺服器憑證不受安裝自我裝載 IR 的電腦所信任。

#### <a name="resolution"></a>解決方案

- 原因1：請確定已安裝自我裝載 IR 的電腦會信任 Data Factory 伺服器憑證及其憑證鏈。
- 原因2：信任自我裝載 IR 電腦上取代的根 CA，或設定 proxy 不要取代 Data Factory 的伺服器憑證。

如需有關在 Windows 上信任憑證的詳細資訊，請參閱 [安裝受信任的根憑證](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)。

#### <a name="additional-information"></a>其他資訊
我們已推出新的 SSL 憑證，此憑證是從 DigiCert 簽署的。 查看 DigiCert 全域根 G2 是否位於受信任的根 CA 中。

  ![螢幕擷取畫面，顯示 [信任的根憑證授權單位] 目錄中的 [DigiCert Global 根目錄 G2] 資料夾。](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

如果它不在信任的根 CA 中，請在 [此下載](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )。 


## <a name="self-hosted-ir-sharing"></a>自我裝載 IR 共用

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>不支援從不同的租使用者共用自我裝載 IR 

#### <a name="symptoms"></a>徵兆

您可能會注意到在不同的租使用者上 (其他資料處理站) 因為您嘗試從 Azure Data Factory UI 共用自我裝載的 IR，但無法在不同租使用者上的資料處理站之間共用它。

#### <a name="cause"></a>原因

無法跨租使用者共用自我裝載的 IR。

## <a name="next-steps"></a>後續步驟

如需疑難排解的詳細資訊，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&頁面](/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [對應資料流程效能指南](concepts-data-flow-performance.md)