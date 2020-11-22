---
title: Azure AD Connect 雲端布建疑難排解
description: 本文說明如何針對雲端布建代理程式可能發生的問題進行疑難排解。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: fa7292d423d8b716ffd75a1a20431fb5a79bbf96
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237335"
---
# <a name="cloud-provisioning-troubleshooting"></a>雲端布建疑難排解

雲端布建觸及許多不同的專案，並具有許多不同的相依性。 這種廣泛的範圍可能會導致各種問題。 本文可協助您針對這些問題進行疑難排解。 它引進了您要專注的一般區域、如何收集其他資訊，以及您可以用來追蹤問題的各種技術。


## <a name="common-troubleshooting-areas"></a>常見的疑難排解區域

|Name|說明|
|-----|-----|
|[代理程式問題](#agent-problems)|確認代理程式已正確安裝，而且與 Azure Active Directory (Azure AD) 進行通訊。|
|[物件同步處理問題](#object-synchronization-problems)|使用布建記錄來針對物件同步處理問題進行疑難排解。|
|[布建隔離的問題](#provisioning-quarantined-problems)|瞭解布建隔離問題以及如何修正這些問題。|


## <a name="agent-problems"></a>代理程式問題
您要向代理程式確認的一些第一個事項如下：

-  是否已安裝？
-  代理程式是在本機執行嗎？
-  代理程式是在入口網站中嗎？
-  代理程式是否標記為狀況良好？

這些專案可以在執行代理程式的 Azure 入口網站和本機伺服器上進行驗證。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證

若要確認 Azure 已看到代理程式，且狀況良好，請遵循下列步驟。

1. 登入 Azure 入口網站。
1. 從左側選取 [Azure Active Directory]  > [Azure AD Connect]。 在中間選取 [管理佈建 (預覽)]。
1. 在 [Azure AD 佈建 (預覽)] 畫面上，選取 [檢閱所有代理程式]。

   ![檢查所有代理程式](media/how-to-install/install-7.png)</br>
 
1. 在 [ **內部部署布建代理** 程式] 畫面上，您會看到已安裝的代理程式。 確認有問題的代理程式存在，而且標示為「 *狀況良好*」。

   ![內部部署佈建代理程式畫面](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>驗證埠

確認 Azure 正在接聽埠443，且您的代理程式可以與其通訊。 

此測試會確認您的代理程式可以透過埠443與 Azure 進行通訊。 開啟瀏覽器，然後從安裝代理程式的伺服器上移至先前的 URL。

![驗證通訊埠是否可連線](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>在本機伺服器上

若要確認代理程式正在執行中，請遵循下列步驟。

1. 在安裝代理程式的伺服器上，藉由流覽至 [**服務**] 或移至 [**開始**  >  **執行**  >  **services.msc**] 來開啟服務。
1. 在 [服務] 底下，確定 **Microsoft Azure AD Connect 代理程式更新程式** 和 **Microsoft Azure AD Connect 佈建代理程式** 皆位於該處，且狀態為 [執行中]。

   ![服務畫面](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>常見的代理程式安裝問題

下列各節說明一些常見的代理程式安裝問題和一般解決方法。

#### <a name="agent-failed-to-start"></a>代理程式無法啟動

您可能會收到錯誤訊息，指出：

**無法啟動服務 ' Microsoft Azure AD Connect 布建代理程式。確認您有足夠的許可權可以啟動系統服務。** 

此問題通常是因為群組原則導致無法將許可權套用至安裝程式所建立的本機 NT 服務登入帳戶 (NT SERVICE\AADConnectProvisioningAgent) 。 需要有這些權限，才能啟動服務。

若要解決此問題，請遵循下列步驟。

1. 使用管理員帳戶登入伺服器。
1. 瀏覽至 [服務]，或前往 [開始] > [執行] > [Services.msc]，以開啟 [服務]。
1. 在 [ **服務**] 下，按兩下 [Microsoft Azure AD 連線布建 **代理程式]**。
1. 在 [ **登** 入] 索引標籤上，將 **此帳戶** 變更為網域系統管理員。然後重新開機服務。 

   ![[登入] 索引標籤](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理程式超時或憑證無效

當您嘗試註冊代理程式時，可能會收到下列錯誤訊息。

![逾時錯誤訊息](media/how-to-troubleshoot/troubleshoot4.png)

此問題通常是因為代理程式無法連線到混合式識別服務所造成的，而需要您設定 HTTP Proxy。 若要解決此問題，請設定輸出 Proxy。 

布建代理程式支援使用輸出 proxy。 您可以藉由編輯代理程式設定檔 *C:\Program Files\Microsoft Azure AD Connect* 布建 Agent\AADConnectProvisioningAgent.exe.config來設定它。將下列幾行加入至結束記號之前的檔案結尾 `</configuration>` 。
`[proxy-server]` `[proxy-port]` 以您的 proxy 伺服器名稱和埠值取代變數。

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>代理程式註冊失敗，發生安全性錯誤

當您安裝雲端布建代理程式時，可能會收到錯誤訊息。

此問題通常是因為本機 PowerShell 執行原則導致代理程式無法執行 PowerShell 註冊腳本所造成。

若要解決此問題，請變更伺服器上的 PowerShell 執行原則。 您必須將電腦和使用者原則設定為 *未定義* 或 *RemoteSigned*。 如果設定為 [不 *受限制*]，您將會看到此錯誤。 如需詳細資訊，請參閱 [PowerShell 執行原則](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)。 

### <a name="log-files"></a>記錄檔

根據預設，代理程式會發出最少的錯誤訊息和堆疊追蹤資訊。 您可以在 **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect** 布建 Agent\Trace 資料夾中找到這些追蹤記錄。

若要收集與代理程式相關問題疑難排解的其他詳細資料，請遵循下列步驟。

1.  安裝 AADCloudSyncTools PowerShell 模組[，如下所述。](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. 使用 `Export-AADCloudSyncToolsLogs` PowerShell Cmdlet 來捕捉資訊。  您可以使用下列參數來微調資料收集。
      - SkipVerboseTrace 只會在未捕獲詳細資訊記錄的情況下匯出目前的記錄檔 (預設值 = false) 
      - TracingDurationMins 指定不同的捕獲期間 (預設值 = 3 分鐘) 
      - OutputPath 指定不同的輸出路徑 (預設 = 使用者的檔) 


## <a name="object-synchronization-problems"></a>物件同步處理問題

下一節包含針對物件同步處理進行疑難排解的資訊。

### <a name="provisioning-logs"></a>佈建記錄

在 Azure 入口網站中，布建記錄可以用來協助追蹤和疑難排解物件同步處理問題。 若要查看記錄，請選取 [ **記錄**]。

![記錄按鈕](media/how-to-troubleshoot/log1.png)

布建記錄可提供您內部部署 Active Directory 環境與 Azure 之間同步處理之物件狀態的豐富資訊。

![布建記錄畫面](media/how-to-troubleshoot/log2.png)

您可以使用頁面頂端的下拉式方塊，在特定問題（例如日期）上將視圖篩選為零。 按兩下個別事件以查看其他資訊。

![布建記錄下拉式清單方塊資訊](media/how-to-troubleshoot/log3.png)

這項資訊會提供詳細的步驟，以及發生同步處理問題的地方。 如此一來，您就可以找出問題的確切點。


## <a name="provisioning-quarantined-problems"></a>布建隔離的問題

雲端布建會監視設定的健康情況，並將狀況不良的物件置於隔離狀態。 如果對目標系統進行的大部分或所有呼叫一直都因為錯誤而失敗（例如，不正確系統管理員認證），則布建作業會標示為隔離。

![隔離狀態](media/how-to-troubleshoot/quarantine1.png)

您可以藉由選取狀態來查看隔離的其他相關資訊。 您也可以取得錯誤碼和訊息。

![隔離狀態資訊](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>解析隔離

- 使用 Azure 入口網站重新開機布建作業。 在 [代理程式設定] 頁面上，選取 [ **重新開機** 布建]。

  ![重新開機布建](media/how-to-troubleshoot/quarantine3.png)

- 使用 Microsoft Graph [重新開機](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)布建作業。 您將可完全掌控重新開機的內容。 您可以選擇清除：
  - Escrows，重新開機會在隔離狀態中進行累算的「證書計數器」。
  - 隔離，以移除隔離的應用程式。
  - 浮水印。 
  
  使用下列要求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>修復雲端同步服務帳戶
如果您需要修復雲端同步服務帳戶，您可以使用 `Repair-AADCloudSyncToolsAccount` 。  


   1.  使用 [此處](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) 所述的安裝步驟開始，然後繼續進行其餘的步驟。
   2.  從具有系統管理許可權的 Windows PowerShell 會話中，輸入或複製並貼上下列內容： 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. 輸入您的 Azure AD 全域管理員認證
   4. 輸入或複製並貼上下列內容： 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. 一旦完成此作業，就應該會指出帳戶已順利修復。

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
