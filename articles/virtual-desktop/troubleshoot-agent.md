---
title: 針對 Windows 虛擬桌面代理程式問題進行疑難排解-Azure
description: 如何解決常見的代理程式和連線能力問題。
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 5f9d772a4cc5722201891450707a68fe487acc3a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540564"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>針對常見的 Windows 虛擬桌面代理程式問題進行疑難排解

Windows 虛擬桌面代理程式可能會因為多個因素而導致連線問題：
   - 代理程式上的錯誤，導致代理程式停止服務。
   - 更新的問題。
   - 在代理程式安裝期間安裝的問題，會中斷與會話主機的連接。

本文將引導您瞭解這些常見案例的解決方案，以及如何解決連接問題。

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>錯誤： RDAgentBootLoader 及/或遠端桌面代理程式載入器已停止執行

如果您看到下列任何問題，這表示載入代理程式的開機載入器無法正確安裝代理程式，且代理程式服務未執行：
- **RDAgentBootLoader** 可能已停止或未執行。
- **遠端桌面代理程式載入** 器沒有任何狀態。

若要解決此問題，請啟動 RDAgent boot 載入器：

1. 在 [服務] 視窗中，在 [ **遠端桌面代理程式載入** 器] 上按一下滑鼠右鍵。
2. 選取 [開始]。 如果此選項呈現灰色，表示您沒有系統管理員許可權，必須取得這些許可權才能啟動服務。
3. 等候10秒鐘，然後以滑鼠右鍵按一下 [ **遠端桌面代理程式載入** 器]。
4. 選取 [重新整理]。
5. 如果服務在您啟動並重新整理之後停止，則可能是註冊失敗。 如需詳細資訊，請參閱 [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)。

## <a name="error-invalid_registration_token"></a>錯誤： INVALID_REGISTRATION_TOKEN

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為3277的事件，也就是描述中的 **INVALID_REGISTRATION_TOKEN** ，表示您的註冊權杖未辨識為有效。

若要解決此問題，請建立有效的註冊權杖：

1. 若要建立新的註冊權杖，請依照「為 [VM 產生新的註冊金鑰](#step-3-generate-a-new-registration-key-for-the-vm) 」一節中的步驟執行。
2. 開啟登錄編輯程式。 
3. 前往 **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent**。
4. 選取 [ **IsRegistered**]。 
5. 在 [ **數值資料：** 輸入] 方塊中，輸入 **0** ，然後選取 **[確定]**。 
6. 選取 [ **RegistrationToken**]。 
7. 在 [ **數值資料：** 輸入] 方塊中，貼上步驟1中的註冊權杖。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 0 的螢幕擷取畫面](media/isregistered-token.png)

8. 以系統管理員身分開啟命令提示字元。
9. 輸入 **net Stop RDAgentBootLoader**。 
10. 輸入 **net Start RDAgentBootLoader**。 
11. 開啟登錄編輯程式。
12. 前往 **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent**。
13. 確認 **IsRegistered** 設定為1，而且資料行中沒有任何資料行可供 **RegistrationToken**。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 1 的螢幕擷取畫面](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form-or-not_found-url"></a>錯誤：代理程式無法使用 INVALID_FORM 或 NOT_FOUND 連接到 broker。 URL

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為3277的事件，表示 **INVALID_FORM** 或 **NOT_FOUND。** 描述中的 URL，代理程式與訊息代理程式之間的通訊發生錯誤。 代理程式無法連線到訊息代理程式，因此無法連線到特定的 URL。 這可能是因為您的防火牆或 DNS 設定。

若要解決此問題，請確認您可以連線至 BrokerURI 和 BrokerURIGlobal：
1. 開啟 [登錄編輯程式]。 
2. 前往 **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent**。 
3. 記下 **BrokerURI** 和 **BrokerURIGlobal** 的值。

   > [!div class="mx-imgBorder"]
   > ![Broker uri 和 broker uri global 的螢幕擷取畫面](media/broker-uri.png)

 
4. 開啟瀏覽器並移至 [ *\<BrokerURI\> api/健康* 情況]。 
   - 請確定您使用 **BrokerURI** 中步驟3的值。 在本節的範例中，它會是 <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> 。
5. 在瀏覽器中開啟另一個索引標籤，然後移至 [ *\<BrokerURIGlobal\> api/健康* 情況]。 
   - 請確定您使用 **BrokerURIGlobal** 連結中步驟3的值。 在本節的範例中，它會是 <https://rdbroker.wvd.microsoft.com/api/health> 。
6. 如果網路未封鎖 broker 連線，這兩個頁面將會順利載入，而且會顯示一則訊息，指出「 **RD Broker 狀況良好** 」，如下列螢幕擷取畫面所示。 

   > [!div class="mx-imgBorder"]
   > ![已成功載入 broker uri 存取權的螢幕擷取畫面](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![已成功載入 broker 全域 uri 存取權的螢幕擷取畫面](media/broker-global.png)
 

7. 如果網路封鎖 broker 連線，將不會載入頁面，如下列螢幕擷取畫面所示。 

   > [!div class="mx-imgBorder"]
   > ![未成功載入的 broker 存取的螢幕擷取畫面](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![未成功載入 broker 全域存取的螢幕擷取畫面](media/unsuccessful-broker-global.png)

8. 如果網路封鎖這些 Url，您將需要解除封鎖所需的 Url。 如需詳細資訊，請參閱 [必要的 URL 清單](safe-url-list.md)。
9. 如果這無法解決您的問題，請確定您沒有任何群組原則的密碼會封鎖代理程式以進行訊息代理程式連線。 Windows 虛擬桌面使用與 [Azure Front Door](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door)相同的 TLS 1.2 密碼。 如需詳細資訊，請參閱 [連接安全性](network-connectivity.md#connection-security)。

## <a name="error-3703-or-3019"></a>錯誤：3703或3019

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為3703的事件，指出 **RD 閘道 Url：無法存取** ，或在描述中有識別碼為3019的任何事件，則代理程式無法連線到閘道 url 或 web 通訊端傳輸 url。 若要成功連接到您的工作階段主機，並允許這些端點的網路流量略過限制，您必須解除封鎖所 [需 Url 清單](safe-url-list.md)中的 url。 此外，請確定您的防火牆或 proxy 設定不會封鎖這些 Url。 必須將這些 Url 解除封鎖，才能使用 Windows 虛擬桌面。

若要解決此問題，請確認您的防火牆及/或 DNS 設定未封鎖這些 Url：
1. [使用 Azure 防火牆來保護 Windows 虛擬桌面部署。](../firewall/protect-windows-virtual-desktop.md)
2. 設定您的 [Azure 防火牆 DNS 設定](../firewall/dns-settings.md)。

## <a name="error-installmsiexception"></a>錯誤： InstallMsiException

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為3277的事件，這會在描述中顯示 **InstallMsiException** ，則在您嘗試安裝代理程式時，安裝程式已在執行另一個應用程式，或原則封鎖了 msiexec.exe 程式正在執行。

若要解決此問題，請停用下列原則：
   - 關閉 Windows Installer  
      - 類別路徑：電腦配置組 \Windows 元件 \Windows 安裝程式
   
>[!NOTE]
>這並不是完整的原則清單，而是我們目前所知的原則。

若要停用原則：
1. 以系統管理員身分開啟命令提示字元。
2. 輸入並執行 [ **services.msc**]。
3. 在彈出的 [ **原則結果組** ] 視窗中，移至類別路徑。
4. 選取原則。
5. 選取 [已停用]。
6. 選取 [套用]  。   

   > [!div class="mx-imgBorder"]
   > ![原則結果組中 Windows Installer 原則的螢幕擷取畫面](media/gpo-policy.png)

## <a name="error-win32exception"></a>錯誤： Win32Exception

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為3277的事件，這會在描述中顯示 **InstallMsiException** ，表示原則會封鎖 cmd.exe 無法啟動。 封鎖此程式會讓您無法執行主控台視窗，這是您在代理程式每次更新時，需要用來重新開機服務的專案。

若要解決此問題，請停用下列原則：
   - 防止存取命令提示字元   
      - 類別路徑：使用者配置組 \ 系統
    
>[!NOTE]
>這並不是完整的原則清單，而是我們目前所知的原則。

若要停用原則：
1. 以系統管理員身分開啟命令提示字元。
2. 輸入並執行 [ **services.msc**]。
3. 在彈出的 [ **原則結果組** ] 視窗中，移至類別路徑。
4. 選取原則。
5. 選取 [已停用]。
6. 選取 [套用]  。   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>錯誤： Stack 接聽程式在 Windows 10 2004 VM 上無法運作

在命令提示字元中執行 **qwinsta** ，並記下出現在 **rdp sxs** 旁的版本號碼。 如果您未看到 **rdp-tcp** 和 **rdp sxs** 元件旁邊有 **接聽** ，或是在執行 **qwinsta** 之後完全沒有顯示，則表示有堆疊問題。 堆疊更新會隨代理程式更新一起安裝，而當此安裝困難時，Windows 虛擬桌面接聽程式將無法運作。

若要解決此問題：
1. 開啟登錄編輯程式。
2. 移至 **HKEY_LOCAL_MACHINE**  >  **系統**  >  **CurrentControlSet**  >  **控制**  >  **終端機伺服器**  >  **WinStations**。
3. 在 [ **WinStations** ] 底下，您可能會看到幾個不同堆疊版本的資料夾，請選取符合您在命令提示字元中執行 **qwinsta** 時所看到的版本資訊的資料夾。
4. 尋找 **fReverseConnectMode** 並確定其資料值為 **1**。 也請確定 **fEnableWinStation** 設定為 **1**。

   > [!div class="mx-imgBorder"]
   > ![FReverseConnectMode 的螢幕擷取畫面](media/fenable-2.png)

5. 如果 **fReverseConnectMode** 未設定為 **1**，請選取 [ **fReverseConnectMode** ]，然後在其 [值] 欄位中輸入 **1** 。 
6. 如果 **fEnableWinStation** 未設定為 **1**，請選取 [ **fEnableWinStation** ]，然後在其 [值] 欄位中輸入 **1** 。
7. 重新啟動您的 VM。 

>[!NOTE]
>若要一次變更多個 Vm 的 **fReverseConnectMode** 或 **fEnableWinStation** 模式，您可以執行下列兩項作業之一：
>
>- 從您已有工作的電腦匯出登錄機碼，並將它匯入至所有其他需要此變更的電腦。
>- 建立 (GPO) 的一般原則物件，以設定需要變更之電腦的登錄機碼值。

7. 移至 **HKEY_LOCAL_MACHINE**  >  **系統**  >  **CurrentControlSet**  >  **控制**  >  **終端機伺服器**  >  **ClusterSettings**。
8. 在 [ **ClusterSettings**] 底下，尋找 [ **SessionDirectoryListener** ]，並確定其資料值為 [ **rdp-sxs**...]。
9. 如果 **SessionDirectoryListener** 未設定為 **rdp**，您將需要依照 [ [卸載代理程式和開機載入](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) 器] 一節中的步驟，先卸載代理程式、開機載入器和堆疊元件，然後 [重新安裝代理程式和開機載入](#step-4-reinstall-the-agent-and-boot-loader)器。 這會重新安裝並存堆疊。

## <a name="error-users-keep-getting-disconnected-from-session-hosts"></a>錯誤：使用者持續與會話主機中斷連線

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為0的事件，這表示描述中的 **CheckSessionHostDomainIsReachableAsync** 和/或使用者持續中斷與其工作階段主機的連線，則您的伺服器不會從 Windows 虛擬桌面服務取得心跳。

若要解決此問題，請變更 [心跳閾值]：
1. 以系統管理員身分開啟命令提示字元。
2. 輸入 **qwinsta** 命令，然後執行它。
3. 應該會顯示兩個堆疊元件： **rdp-tcp** 和 **rdp**。 
   - 視您使用的作業系統版本而定， **rdp** 可能會接著組建編號，如下列螢幕擷取畫面所示。 如果是，請務必將此數位寫下來以供日後之用。
4. 開啟登錄編輯程式。
5. 移至 **HKEY_LOCAL_MACHINE**  >  **系統**  >  **CurrentControlSet**  >  **控制**  >  **終端機伺服器**  >  **WinStations**。
6. 在 **WinStations** 下，您可能會看到不同堆疊版本的數個資料夾。 選取與步驟3中的版本號碼相符的資料夾。
7. 以滑鼠右鍵按一下 [登錄編輯程式]，然後選取 [**新**  >  **的 dword (32-位) 值**]，以建立新的登錄 DWORD。 當您建立 DWORD 時，請輸入下列值：
   - HeartbeatInterval：10000
   - HeartbeatWarnCount：30 
   - HeartbeatDropCount：60 
8. 重新啟動您的 VM。

## <a name="error-downloadmsiexception"></a>錯誤： DownloadMsiException

移至 **事件檢視器**  >  **Windows 記錄**  >  **應用程式**。 如果您看到識別碼為3277的事件，這會在描述中顯示 **DownloadMsiException** ，則磁片上沒有足夠的空間可供 RDAgent。

若要解決此問題，請在磁片上建立空間，方法如下：
   - 刪除不再存在於使用者中的檔案
   - 增加 VM 的儲存體容量

## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>錯誤： Vm 卡在無法使用或升級狀態

以系統管理員身分開啟 PowerShell 視窗，然後執行下列 Cmdlet：

```powershell
Get-AzWvdSessionHost -TenantName <tenantname> -HostPoolName <hostpoolname>|Select-Object*
```

如果為主機集區中的工作階段主機或主機列出的狀態一律顯示為 [無法 **使用** ] 或 [正在 **升級**]，表示代理程式或堆疊安裝可能已失敗

若要解決此問題，請重新安裝並存堆疊：
1. 以系統管理員身分開啟命令提示字元。
2. 輸入 **net Stop RDAgentBootLoader**。 
3. 移至 [控制台]   >  [程式]   >  [程式和功能]  。
4. 卸載 **遠端桌面服務 SxS 網路堆疊** 的最新版本，或 ReverseConnectListener 底下 **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** 中所列的版本。
5. 以系統管理員身分開啟主控台視窗，然後移至 **Program Files**  >  **Microsoft RDInfra**。
6. 選取 **SxSStack** 元件，或執行 **msiexec/i SxSStack- <version> .MSI** 命令以安裝 msi。
8. 重新啟動您的 VM。
9. 返回至命令提示字元，並執行 **qwinsta** 命令。
10. 確認步驟6中所安裝的堆疊元件旁邊會顯示 [ **接聽** ]。
   - 若是如此，請在命令提示字元中輸入 **net Start RDAgentBootLoader** ，然後重新開機您的 VM。
   - 如果不是，您必須 [重新註冊您的 VM，並重新安裝代理程式](#your-issue-isnt-listed-here-or-wasnt-resolved) 元件。

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>錯誤：找不到連線： RDAgent 沒有與 broker 的使用中連接

您的 Vm 可能會達到其連線限制，因此 VM 無法接受新的連線。

若要解決此問題：
   - 減少最大會話限制。 這可確保資源會更平均地分散到工作階段主機，而且會導致資源耗盡。
   - 增加 Vm 的資源容量。

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>錯誤：操作 Pro VM 或其他不支援的 OS

只有 Windows Enterprise 或 Windows Server Sku 支援並存堆疊，這表示不支援 Pro VM 等作業系統。 如果您沒有企業或伺服器 SKU，此堆疊將會安裝在您的 VM 上，但不會啟動，因此當您在命令列中執行 **qwinsta** 時，將不會看到它顯示。

若要解決此問題，請建立 Windows Enterprise 或 Windows Server 的 VM。
1. 移至 [虛擬機器詳細資料](create-host-pools-azure-marketplace.md#virtual-machine-details) ，並遵循步驟1-12 來設定下列其中一個建議的映射：
   - Windows 10 企業版多個會話，版本1909
   - Windows 10 企業版多會話版本 1909 + Microsoft 365 應用程式
   - Windows Server 2019 Datacenter
   - Windows 10 企業版多個會話，版本2004
   - Windows 10 企業版多會話版本 2004 + Microsoft 365 應用程式
2. 選取 [ **審核並建立**]。

## <a name="error-name_already_registered"></a>錯誤： NAME_ALREADY_REGISTERED

您的 VM 名稱已經註冊，而且可能是重複的。

若要解決此問題：
1. 遵循 [從主機集區移除工作階段主機](#step-2-remove-the-session-host-from-the-host-pool) 一節中的步驟。
2. [建立另一個 VM](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal)。 請務必為此 VM 選擇唯一的名稱。
3. 移至 [Azure 入口網站] (https://portal.azure.com) ，然後開啟 VM 所在主機集區的 [ **總覽** ] 頁面。 
4. 開啟 [ **工作階段主機** ] 索引標籤，並檢查以確定所有的工作階段主機都在該主機集區中。
5. 請等候5-10 分鐘，讓工作階段主機狀態顯示為「 **可用**」。

   > [!div class="mx-imgBorder"]
   > ![可用工作階段主機的螢幕擷取畫面](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>您的問題未列于此處或未解決

如果您在本文中找不到您的問題，或指示無法協助您，建議您卸載、重新安裝，然後重新註冊 Windows 虛擬桌面代理程式。 本節中的指示將說明如何藉由卸載所有代理程式、開機載入器和堆疊元件、從主機集區中移除工作階段主機、為 VM 產生新的註冊金鑰，以及重新安裝代理程式和開機載入器，來將您的 VM 重新註冊至 Windows 虛擬桌面服務。 如果下列一或多個案例適用于您，請依照下列指示進行：
- 您的 VM 停滯于 **升級** 或 **無法使用**
- 您的堆疊接聽程式無法運作，且您正在 Windows 10 1809、1903或1904上執行
- 您收到 **EXPIRED_REGISTRATION_TOKEN** 錯誤
- 您在工作階段主機清單中看不到您的 Vm
- 您在 [服務] 視窗中看不到 **遠端桌面代理程式載入** 器
- 您在工作管理員中看不到 **RdAgentBootLoader** 元件
- 本文中的指示並未解決您的問題

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>步驟1：卸載所有代理程式、開機載入器和堆疊元件程式

重新安裝代理程式、開機載入器和堆疊之前，您必須先從 VM 卸載任何現有的元件程式。 卸載所有代理程式、開機載入器和堆疊元件程式：
1. 以系統管理員身分登入您的 VM。 
2. 移至 [控制台]   >  [程式]   >  [程式和功能]  。
3. 移除下列程式：
   - 遠端桌面代理程式開機載入器
   - 遠端桌面服務基礎結構代理程式
   - 遠端桌面服務基礎結構 Geneva 代理程式
   - 遠端桌面服務 SxS 網路堆疊
   
>[!NOTE]
>您可能會看到這些程式有多個實例。 請務必移除所有的專案。

   > [!div class="mx-imgBorder"]
   > ![卸載程式的螢幕擷取畫面](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>步驟2：從主機集區中移除工作階段主機

當您從主機集區中移除工作階段主機時，工作階段主機將不再向該主機集區註冊。 這會做為工作階段主機註冊的重設。 若要從主機集區中移除工作階段主機：
1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 VM 所在的主機集區的 [**總覽**] 頁面。 
2. 移至 [ **工作階段主機** ] 索引標籤，以查看該主機集區中所有工作階段主機的清單。
3. 查看工作階段主機清單，然後選取您想要移除的 VM。
4. 選取 [移除]。  

   > [!div class="mx-imgBorder"]
   > ![從主機集區中移除 VM 的螢幕擷取畫面](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>步驟3：為 VM 產生新的註冊金鑰

您必須產生新的註冊金鑰，用來將您的 VM 重新註冊至主機集區和服務。 若要為 VM 產生新的註冊金鑰：
1. 開啟 [Azure 入口網站](https://portal.azure.com) 並移至您想要編輯之 VM 的主機集區的 [ **總覽** ] 頁面。
2. 選取 [ **註冊金鑰**]。

   > [!div class="mx-imgBorder"]
   > ![入口網站中註冊金鑰的螢幕擷取畫面](media/reg-key.png)

3. 開啟 [ **註冊金鑰** ] 索引標籤，然後選取 [ **產生新的金鑰**]。
4. 輸入到期日，然後選取 **[確定]**。  

>[!NOTE]
>到期日不能少於一小時，且不能超過其產生時間和日期的27天。 強烈建議您將到期日設為最長27天。

5. 將新產生的金鑰複製到剪貼簿。 您稍後將需要此金鑰。

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>步驟4：重新安裝代理程式和開機載入器

藉由重新安裝最新版的代理程式和開機載入器，並存堆疊和 Geneva 監視代理程式也會自動安裝。 若要重新安裝代理程式和開機載入器：
1. 以系統管理員身分登入您的 VM，並遵循 [註冊虛擬機器](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) 中的指示，下載 **Windows 虛擬桌面代理程式** 和 **windows 虛擬桌面代理程式** 開機載入器。

   > [!div class="mx-imgBorder"]
   > ![代理程式和開機載入器下載頁面的螢幕擷取畫面](media/download-agent.png)

2. 以滑鼠右鍵按一下您剛剛下載的代理程式和開機載入器安裝程式。
3. 選取 [屬性] 。
4. 選取 [Unblock] \(解除封鎖\)。
5. 選取 [確定]  。
6. 執行代理程式安裝程式。
7. 當安裝程式要求您輸入註冊權杖時，請貼上剪貼簿中的註冊金鑰。 

   > [!div class="mx-imgBorder"]
   > ![貼上註冊權杖的螢幕擷取畫面](media/pasted-agent-token.png)

8. 執行開機載入器安裝程式。
9. 重新啟動您的 VM。 
10. 移至 [Azure 入口網站](https://portal.azure.com) ，並開啟 VM 所屬主機集區的 [ **總覽** ] 頁面。
11. 移至 [ **工作階段主機** ] 索引標籤，以查看該主機集區中所有工作階段主機的清單。
12. 您現在應該會看到已在主機集區中登錄的工作階段主機，以及 **可用** 的狀態。 

   > [!div class="mx-imgBorder"]
   > ![可用工作階段主機的螢幕擷取畫面](media/hostpool-portal.png)

## <a name="next-steps"></a>後續步驟

如果問題持續發生，請建立支援案例，並包含您所遇到問題的詳細資訊，以及您嘗試解決此問題所採取的任何動作。 下列清單包含您可以用來對 Windows 虛擬桌面部署中的問題進行疑難排解的其他資源。

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立主機集區時所發生的問題進行疑難排解，請參閱 [環境和主機集區建立](troubleshoot-set-up-issues.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要對 Windows 虛擬桌面用戶端連線問題進行疑難排解，請參閱 [Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱 [疑難排解遠端桌面用戶端](troubleshoot-client.md)。
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解此服務，請參閱 [Windows 虛擬桌面環境](environment-setup.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../azure-resource-manager/templates/deployment-history.md)。
