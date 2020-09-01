---
title: 使用 Azure 入口網站對 GPU 進行 Azure Stack Edge 的疑難排解 |Microsoft Docs
description: 說明如何針對 Azure Stack Edge GPU 問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: alkohli
ms.openlocfilehash: 22f0c63c2b60b6c72ad297492045df17e10dd06c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268317"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-gpu-device"></a>針對 Azure Stack Edge GPU 裝置上的問題進行疑難排解 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明如何針對 Azure Stack Edge GPU 裝置上的問題進行疑難排解。 


## <a name="run-diagnostics"></a>執行診斷

若要診斷任何裝置錯誤並進行疑難排解，您可以執行診斷測試。 在您裝置的本機 Web UI 中執行下列步驟，以執行診斷測試。

1. 在本機 Web UI 中，移至 [疑難排解] > [診斷測試]****。 選取您要執行的測試，然後選取 [ **執行測試**]。 這會執行測試，以診斷您的網路、裝置、Web Proxy、時間或雲端設定的任何可能問題。 您會收到裝置正在執行測試的通知。

    ![選取測試 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. 測試完成後會顯示結果。 

    ![檢視測試結果](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    如果測試失敗，則會顯示建議動作的 URL。 選取 URL 以查看建議的動作。
 
    ![檢閱失敗測試的警告](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>收集支援套件

記錄套件包含有助於 Microsoft 支援小組疑難排解任何裝置問題的所有相關記錄。 您可以透過本機 Web UI 產生記錄套件。

執行下列步驟來收集支援套件。 

1. 在本機 Web UI 中，移至 [疑難排解] > [支援]****。 選取 [ **建立支援套件**]。 系統會開始收集支援套件。 套件收集可能需要幾分鐘的時間。

    ![選取新增使用者](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. 建立支援封裝之後，請選取 [ **下載支援套件**]。 壓縮的套件會下載至您所選的路徑。 您可以將此套件解壓縮並檢視系統記錄檔。

    ![選取新增使用者](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>收集 advanced security 記錄

Advanced security 記錄可以是 Azure Stack Edge 裝置的軟體或硬體入侵記錄。

### <a name="software-intrusion-logs"></a>軟體入侵記錄

系統會針對輸入和輸出流量收集軟體入侵或預設防火牆記錄檔。 

- 當裝置在工廠製作映射時，會啟用預設的防火牆記錄。 當您透過本機 UI 或透過裝置的 Windows PowerShell 介面建立支援封裝時，預設會將這些記錄套件配套至支援封裝。

- 如果支援套件中只需要防火牆記錄來檢查裝置上的任何軟體 (NW) 入侵，則 `-Include FirewallLog` 在建立支援封裝時請使用選項。 

- 如果未提供特定的 include 選項，就會在支援套件中包含防火牆記錄檔作為預設值。

- 在支援封裝中，防火牆記錄檔是，位於 `pfirewall.log` 根資料夾中。 以下是 Azure Stack Edge 裝置的軟體入侵記錄範例。 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>硬體入侵記錄

若要偵測裝置的任何硬體入侵，目前會記錄所有的底座事件，例如開啟或關閉底座。 

- 使用 Cmdlet 讀取裝置的系統事件記錄檔 `racadm` 。 然後，這些事件會篩選至檔案中的底座相關事件 `HWIntrusion.txt` 。

- 若只要取得支援套件中的硬體入侵記錄，請在 `-Include HWSelLog` 建立支援封裝時使用選項。 

- 如果未提供特定的 include 選項，就會在支援套件中包含硬體入侵記錄檔作為預設值。

- 在支援套件中，硬體入侵記錄檔是，位於 `HWIntrusion.txt` 根資料夾中。 以下是 Azure Stack Edge 裝置的硬體入侵記錄範例。 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>使用記錄進行疑難排解

在上傳和重新整理程序期間發生的所有錯誤都會包含在各自的錯誤檔案中。

1. 若要查看錯誤檔案，請移至您的共用，然後選取共用以查看內容。 


2. 選取 [ _Microsoft Data Box Edge] 資料夾_。 這個資料夾有兩個子資料夾：

    - 具有上傳錯誤記錄檔的 [上傳] 資料夾。
    - 在重新整理期間發生錯誤的 [重新整理] 資料夾。

    以下是可供重新整理的範例記錄檔。

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 當您在此檔案中看到錯誤 (已在此範例中醒目提示) 時，請記下錯誤碼，在此例中是 16001。 針對下列錯誤參考，查閱此錯誤碼的說明。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>使用錯誤清單進行疑難排解

錯誤清單會從已識別的案例中進行編譯，並可用於自我診斷和疑難排解。 

## <a name="azure-resource-manager"></a>Azure Resource Manager

以下是在 Azure Resource Manager 設定以存取裝置時可能會顯示的錯誤。 

| **問題/錯誤** |  **解決方法** | 
|------------|-----------------|
|一般問題|<li>[確認已正確設定 Edge 裝置](#verify-the-device-is-configured-properly)。<li> [確認用戶端已正確設定](#verify-the-client-is-configured-properly)|
|Add-azurermenvironment：傳送要求時發生錯誤。<br>At line： 1 char：1<br>+ 新增-Add-azurermenvironment-Name Az3-ARMEndpoint " https://management.dbe .。。|此錯誤表示您的 Azure Stack Edge 裝置無法連線或未正確設定。 確認已正確設定 Edge 裝置和用戶端。 如需指引，請參閱此表格中的 **一般問題** 資料列。|
|服務傳回錯誤。 查看 InnerException 以取得更多詳細資料：基礎連接已關閉：無法為 SSL/TLS 安全通道建立信任關係。 |   此錯誤可能是因為一或多個「攜帶您自己的憑證」步驟未正確執行。 您可以在 [這裡](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-connect-resource-manager#step-2-create-and-install-certificates)找到指引。 |
|作業傳回不正確狀態碼 ' ServiceUnavailable ' <br> 回應狀態碼未指出成功： 503 (服務無法使用) 。 | 這項錯誤可能是任何一項條件的結果。<li>ArmStsPool 處於已停止狀態。</li><li>其中一個 Azure Resource Manager/安全性權杖服務網站已關閉。</li><li>Azure Resource Manager 叢集資源已關閉。</li><br><strong>注意：</strong> 重新開機設備可能會修正此問題，但您應該收集支援封裝，讓您可以進一步進行重新調試。|
|AADSTS50126：不正確使用者名稱或密碼。<br>追蹤識別碼：29317da9-52fc-4ba0-9778-446ae5625e5a<br>相互關聯識別碼： 1b9752c4-8cbf-4304-a714-8a16527410f4<br>時間戳記： 2019-11-15 09：21：57Z：遠端伺服器傳回錯誤： (400) 不正確的要求。<br>At line： 1 char：1 |這項錯誤可能是任何一項條件的結果。<li>如果使用者名稱和密碼無效，請遵循 [此處](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-set-azure-resource-manager-password) 的步驟，然後使用正確的密碼，驗證客戶已變更 Azure 入口網站的密碼。<li>針對不正確租使用者識別碼，租使用者識別碼是固定的 GUID，應設定為 `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|連接-Disconnect-azurermaccount： AADSTS90056：資源已停用或不存在。 請檢查您應用程式的程式碼，確保您已經為您嘗試存取的資源指定確切的資源 URL。<br>追蹤識別碼： e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>相互關聯識別碼：75c8ef5a-830e-48b5-b039-595a96488ff9 時間戳記： 2019-11-18 07：00：51Z：遠端伺服器傳回錯誤： (400) 錯誤 |命令中使用的資源端點不 `Add-AzureRmEnvironment` 正確。|
|無法從雲端取得端點。<br>請確定您有網路連線。 錯誤詳細資料： HTTPSConnectionPool (host = ' of4k6suvm.microsoftdatabox.com '，port = 30005) ：超過最大重試次數，url：/metadata/endpoints api-version？ api-version = 2015-01-01 (由 SSLError (SSLError ( 「不正確的信號交換：錯誤 ( [ ( ' SSL 常式 '，' tls_process_server_certificate '，' 憑證驗證失敗 ' ) ]，) "，) ，) # A11 |此錯誤大多出現在 Mac/Linux 環境中，原因是下列問題：<li>未將 PEM 格式憑證新增至 python 憑證存放區。</li> |

### <a name="verify-the-device-is-configured-properly"></a>確認裝置已正確設定

1. 從本機 UI，確認裝置網路的設定是否正確。

2. 確認所有端點的憑證都已更新，如 [這裡](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)所述。

3. 從本機 UI 中的 [ **裝置** ] 頁面取得 Azure Resource Manager 管理和登入端點。

4. 確認裝置已在 Azure 中啟用並註冊。


### <a name="verify-the-client-is-configured-properly"></a>確認用戶端已正確設定

1. 驗證是否已安裝正確的 PowerShell 版本，如 [這裡](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client)所述。

2. 驗證是否已安裝正確的 PowerShell 模組，如 [這裡](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)所述。

3. 驗證可連線 Azure Resource Manager 和登入端點。 您可以嘗試 ping 端點。 例如：

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   如果無法連線，請新增 DNS/主機檔案專案，如 [這裡](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)所述。
   
4. 驗證是否已安裝用戶端憑證，[如下所述。](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)

5. 如果客戶使用的是 PowerShell，您應該執行此 PowerShell 命令，以啟用偵錯工具喜好設定來查看詳細訊息。 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>裝置上的 Blob 儲存體 

以下是與 Azure Stack Edge/資料箱閘道裝置上的 blob 儲存體相關的錯誤。

| **問題/錯誤** |  **解決方法** | 
|--------------------|-----------------|
|無法取出子資源。 其中一個 HTTP 標頭之值的格式不正確。| 從 [ **編輯** ] 功能表中選取 [ **目標 Azure Stack api**]。 然後，重新開機 Azure 儲存體總管。|
|getaddrinfo ENOTFOUND <accountname> blob ... <serialnumber>microsoftdatabox.com|檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至位於此路徑的主機檔案： `C:\Windows\System32\drivers\etc\hosts` 在 Windows 或 `/etc/hosts` Linux 上。|
|無法取出子資源。<br> 詳細資料：自我簽署憑證 |將您裝置的 SSL 憑證匯入 Azure 儲存體總管： <ol><li>從 Azure 入口網站下載憑證。 如需詳細資訊，請參閱 [下載憑證](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)。</li><li>從 [ **編輯** ] 功能表選取 [SSL 憑證]，然後選取 [匯 **入憑證**]。</li></ol>|
|在顯示此錯誤之前，AzCopy 命令似乎停止回應一分鐘：<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至 hosts 檔案，網址為： `C:\Windows\System32\drivers\etc\hosts` 。|
|在顯示此錯誤之前，AzCopy 命令似乎停止回應一分鐘：<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |將裝置的 SSL 憑證匯入系統的憑證存放區中。 如需詳細資訊，請參閱 [下載憑證](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)。|
|AzCopy 命令似乎停止回應20分鐘，再顯示此錯誤：<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至 hosts 檔案，網址為： `/etc/hosts` 。|
|AzCopy 命令似乎停止回應20分鐘，再顯示此錯誤：<br>`Error parsing source location… The SSL connection could not be established`. |將裝置的 SSL 憑證匯入系統的憑證存放區中。 如需詳細資訊，請參閱 [下載憑證](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)。|
|AzCopy 命令似乎停止回應20分鐘，再顯示此錯誤：<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至 hosts 檔案，網址為： `/etc/hosts` 。|
|AzCopy 命令似乎停止回應20分鐘，再顯示此錯誤： `Error parsing source location… The SSL connection could not be established` 。|將裝置的 SSL 憑證匯入系統的憑證存放區中。 如需詳細資訊，請參閱 [下載憑證](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)。|
|其中一個 HTTP 標頭之值的格式不正確。|資料箱不支援已安裝的 Python Microsoft Azure 儲存體程式庫版本。 如需支援的版本，請參閱 Azure 資料箱 Blob 儲存體需求。|
|… [SSL： CERTIFICATE_VERIFY_FAILED] .。。| 執行 Python 之前，請將 REQUESTS_CA_BUNDLE 環境變數設定為 Base64 編碼的 SSL 憑證檔案的路徑 (查看如何 [下載憑證](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)。 例如：<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>或者，將憑證新增至系統的憑證存放區，然後將此環境變數設定為該存放區的路徑。 例如，在 Ubuntu 上︰ <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|連接逾時。|登入 Azure Stack Edge，然後檢查它是否已解除鎖定。 裝置重新開機時，會一直鎖定，直到有人登入為止。|


## <a name="next-steps"></a>後續步驟

- 深入了解[此版本中的已知問題](azure-stack-edge-gpu-2008-release-notes.md)。
