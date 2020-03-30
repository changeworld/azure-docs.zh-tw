---
title: Azure AD 連接雲預配故障排除
description: 本文介紹如何解決雲預配代理可能出現的問題。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549480"
---
# <a name="cloud-provisioning-troubleshooting"></a>雲配置故障排除

雲資源調配涉及許多不同的事物，並且具有許多不同的依賴關係。 這種廣泛的範圍會引起各種問題。 本文可説明您解決這些問題。 它介紹了您需要關注的典型領域、如何收集其他資訊以及可用於跟蹤問題的各種技術。


## <a name="common-troubleshooting-areas"></a>常見故障排除區域

|名稱|描述|
|-----|-----|
|[代理問題](#agent-problems)|驗證代理安裝正確，以及代理是否與 Azure 活動目錄 （Azure AD） 通信。|
|[物件同步問題](#object-synchronization-problems)|使用預配日誌解決物件同步問題。|
|[預配隔離問題](#provisioning-quarantined-problems)|瞭解預配隔離問題以及如何解決這些問題。|


## <a name="agent-problems"></a>代理問題
要與代理驗證的一些第一件事是：

-  是否已安裝？
-  代理是否在本地運行？
-  代理是否位於門戶中？
-  代理是否標記為正常？

這些專案可以在 Azure 門戶和運行代理的本機伺服器上進行驗證。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證

要驗證 Azure 是否看到代理並處於正常狀態，請按照以下步驟操作。

1. 登入 Azure 入口網站。
1. 在左側，選擇**Azure 活動目錄** > **Azure AD 連接**。 在中心中，選擇 **"管理預配（預覽）"。**
1. 在**Azure AD 預配（預覽）** 螢幕上，選擇 **"查看所有代理**"。

   ![查看所有代理](media/how-to-install/install7.png)</br>
 
1. 在**本地預配代理**螢幕上，可以看到已安裝的代理。 驗證有問題的代理是否在那裡，並且標記為 *"正常*"。

   ![本地預配代理螢幕](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>驗證埠

要驗證 Azure 正在偵聽埠 443，並且代理是否可以與它通信，請使用以下工具：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試驗證代理是否可以通過埠 443 與 Azure 通信。 打開瀏覽器，然後從安裝代理的伺服器轉到以前的 URL。

![埠可到達性驗證](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本機伺服器上

要驗證代理是否正在運行，請按照以下步驟操作。

1. 在安裝了代理的伺服器上，通過導航到**它**或訪問**啟動** > **運行** > **服務.msc**打開服務。
1. 在 **"服務**"下，請確保**Microsoft Azure AD 連接代理更新程式**以及**Microsoft Azure AD 連接預配代理**已存在，並且其狀態正在*運行*。

   ![服務螢幕](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>常見的代理安裝問題

以下各節介紹一些常見的代理安裝問題和典型解決方法。

#### <a name="agent-failed-to-start"></a>代理無法啟動

您可能會收到一條錯誤訊息，指出：

**服務"微軟 Azure AD 連接預配代理"無法啟動。驗證您是否有足夠的許可權來啟動系統服務。** 

此問題通常是由群組原則引起的，該策略阻止將許可權應用於安裝程式創建的本地 NT 服務登錄帳戶（NT SERVICE_AADConnect預配代理）。 啟動服務需要這些許可權。

要解決此問題，請按照以下步驟操作。

1. 使用管理員帳戶登錄到伺服器。
1. 通過導航到服務或訪問**啟動** > **運行** > **服務.msc**來打開**服務**。
1. 在 **"服務**"下，按兩下**微軟 Azure AD 連接預配代理**。
1. 在"**登錄**"選項卡上，**將此帳戶**更改為域管理員。然後重新開機服務。 

   ![登錄選項卡](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理超時或證書無效

當您嘗試註冊代理時，您可能會收到以下錯誤訊息。

![逾時錯誤消息](media/how-to-troubleshoot/troubleshoot4.png)

此問題通常是由於代理無法連接到混合標識服務而引起的，需要您配置 HTTP 代理。 要解決此問題，請配置出站代理。 

預配代理支援使用出站代理。 您可以通過編輯代理設定檔*C：\程式檔\微軟 Azure AD 連接預配代理\AADConnect預配代理.exe.config*來配置它。在檔的結尾端向結束`</configuration>`標記之前添加以下行。
將變數`[proxy-server]`替換為`[proxy-port]`代理伺服器名稱和埠值。

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

#### <a name="agent-registration-fails-with-security-error"></a>代理註冊失敗，出現安全錯誤

安裝雲預配代理時可能會收到錯誤訊息。

此問題通常是由於代理由於本地 PowerShell 執行策略而無法執行 PowerShell 註冊腳本引起的。

要解決此問題，更改伺服器上的 PowerShell 執行策略。 您需要將電腦和使用者策略設置為 *"未定義*"或 *"遠端簽名*"。 如果它們設置為 *"無限制"，* 您將看到此錯誤。 有關詳細資訊，請參閱[PowerShell 執行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)。 

### <a name="log-files"></a>記錄檔

預設情況下，代理會發出最少的錯誤訊息和堆疊追蹤資訊。 您可以在資料夾*C：\程式資料\微軟_Azure AD 連接預配代理_跟蹤*中找到這些跟蹤日誌。

要收集有關排除代理相關問題的其他詳細資訊，請按照以下步驟操作。

1. 停止**服務微軟 Azure AD 連接預配代理**。
1. 創建原始設定檔的副本 *：C：\程式檔\微軟 Azure AD 連接預配代理_AADConnect預配代理.exe.config*。
1. 將現有`<system.diagnostics>`部分替換為以下內容，所有跟蹤消息都將轉到檔*ProvAgentTrace.log*。

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. 啟動**服務微軟 Azure AD 連接預配代理**。
1. 使用以下命令尾隨檔和調試問題。 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>物件同步問題

以下部分包含有關對物件同步進行故障排除的資訊。

### <a name="provisioning-logs"></a>佈建記錄

在 Azure 門戶中，預配日誌可用於説明跟蹤和排除物件同步問題。 要查看日誌，請選擇 **"日誌**"。

![記錄按鈕](media/how-to-troubleshoot/log1.png)

預配日誌提供有關本地活動目錄環境和 Azure 之間正在同步的物件的狀態的大量資訊。

![預配日誌螢幕](media/how-to-troubleshoot/log2.png)

您可以使用頁面頂部的下拉清單將視圖篩選為對特定問題（如日期）的零。 按兩下單個事件以查看其他資訊。

![預配日誌下拉清單資訊](media/how-to-troubleshoot/log3.png)

此資訊提供了詳細的步驟以及同步問題發生的位置。 通過這種方式，您可以精確定位問題的確切位置。


## <a name="provisioning-quarantined-problems"></a>預配隔離問題

雲預配監視配置的運行狀況，並將不正常的物件置於隔離狀態。 如果針對目標系統所做的大多數或所有調用始終由於錯誤（例如不正確管理員憑據）而失敗，則預配作業將標記為隔離區。

![隔離狀態](media/how-to-troubleshoot/quarantine1.png)

通過選擇狀態，您可以看到有關隔離的其他資訊。 您還可以獲取錯誤代碼和消息。

![隔離狀態資訊](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>解決隔離

- 使用 Azure 門戶重新開機預配作業。 在代理配置頁上，選擇 **"重新開機預配**"。

  ![重新開機預配](media/how-to-troubleshoot/quarantine3.png)

- 使用 Microsoft 圖形[重新開機預配作業](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 您將完全控制重新開機的內容。 您可以選擇清除：
  - 埃斯庫，重新開機代管櫃檯，累積到隔離狀態。
  - 隔離，從隔離區中刪除應用程式。
  - 浮水印。 
  
  使用下列要求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)



