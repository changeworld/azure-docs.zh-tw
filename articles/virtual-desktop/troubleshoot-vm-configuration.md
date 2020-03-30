---
title: 排除 Windows 虛擬桌面工作階段主機故障 - Azure
description: 在配置 Windows 虛擬桌面工作階段主機虛擬機器時如何解決問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127598"
---
# <a name="session-host-virtual-machine-configuration"></a>設定工作階段主機虛擬機器

使用本文可解決配置 Windows 虛擬桌面工作階段主機虛擬機器 （VM） 時遇到的問題。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="vms-are-not-joined-to-the-domain"></a>VM 未加入域

如果您在將 VM 加入域時遇到問題，請按照這些說明操作。

- 使用[將 Windows 伺服器虛擬機器加入託管域](../active-directory-domain-services/join-windows-vm.md)或使用[域聯接範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)中的過程手動加入 VM。
- 嘗試從 VM 上的命令列 ping 功能變數名稱。
- 查看[故障排除域聯接錯誤訊息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)中的域聯接錯誤訊息的清單。

### <a name="error-incorrect-credentials"></a>錯誤：憑據不正確

**原因：** 在 Azure 資源管理器範本介面修復中輸入憑據時，會進行拼寫錯誤。

**修復：** 採取以下操作之一來解決。

- 手動將 VM 添加到域。
- 確認憑據後重新部署範本。 請參閱[使用 PowerShell 創建主機池](create-host-pools-powershell.md)。
- 使用將現有 Windows VM 加入[AD 域的](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)範本將 VM 加入域。

### <a name="error-timeout-waiting-for-user-input"></a>錯誤：超時等待使用者輸入

**原因：** 用於完成域聯接的帳戶可能具有多重要素驗證 （MFA）。

**修復：** 採取以下操作之一來解決。

- 暫時刪除帳戶的 MFA。
- 使用服務帳戶。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>錯誤：預配期間使用的帳戶沒有完成操作的許可權

**原因：** 由於合規性和法規，正在使用的帳戶沒有將 VM 加入域的許可權。

**修復：** 採取以下操作之一來解決。

- 使用作為管理員組成員的帳戶。
- 向正在使用的帳戶授予必要的許可權。

### <a name="error-domain-name-doesnt-resolve"></a>錯誤：功能變數名稱無法解析

**原因 1：** VM 位於與域所在的虛擬網路 （VNET） 不關聯的虛擬網路上。

**修復 1：** 在預配 VM 的 VNET 和網域控制站 （DC） 運行的 VNET 之間創建 VNET 對等互連。 請參閱[創建虛擬網路對等互連 - 資源管理器，不同的訂閱](../virtual-network/create-peering-different-subscriptions.md)。

**原因 2：** 使用 Azure 活動目錄域服務 （Azure AD DS） 時，虛擬網路沒有更新其 DNS 伺服器設置以指向託管網域控制站。

**修復 2：** 要更新包含 Azure AD DS 的虛擬網路的 DNS 設置，請參閱[更新 Azure 虛擬網路的 DNS 設置](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)。

**原因 3：** 網路介面的 DNS 伺服器設置不指向虛擬網路上的相應 DNS 伺服器。

**修復 3：** 按照 [更改 DNS 伺服器] 中的步驟執行以下操作之一進行解析。
- 使用["更改 DNS"伺服器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)的步驟將網路介面的 DNS 伺服器設置更改為 **"自訂"，** 並指定虛擬網路上的 DNS 伺服器的私人 IP 位址。
- 使用["更改 DNS"伺服器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)的步驟將網路介面的 DNS 伺服器設置更改為**從虛擬網路繼承**，然後使用[更改 DNS 伺服器](../virtual-network/manage-virtual-network.md#change-dns-servers)的步驟更改虛擬網路的 DNS 伺服器設置。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>未安裝 Windows 虛擬桌面代理和 Windows 虛擬桌面引導載入程式

預配 VM 的建議方法是使用 Azure 資源管理器**創建和預配 Windows 虛擬桌面主機池**範本。 該範本會自動安裝 Windows 虛擬桌面代理和 Windows 虛擬桌面代理引導載入程式。

按照這些說明確認已安裝元件並檢查錯誤訊息。

1. 通過簽入**控制台** > **程式和** > **功能**，確認安裝這兩個元件。 如果**Windows 虛擬桌面代理**和**Windows 虛擬桌面代理引導載入程式**不可見，則它們未安裝在 VM 上。
2. 打開**檔資源管理器**並導航到**C：\Windows\Temp_ScriptLog.log**。 如果檔丟失，則表示安裝這兩個元件的 PowerShell DSC 無法在提供的安全上下文中運行。
3. 如果檔**C：_Windows_Temp_ScriptLog.log**存在，請打開該檔並檢查錯誤訊息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>錯誤：缺少 Windows 虛擬桌面代理和 Windows 虛擬桌面代理引導載入程式。 C：\Windows\Temp_ScriptLog.log 也缺少

**原因 1：** 在輸入 Azure 資源管理器範本期間提供的憑據不正確或許可權不足。

**修復 1：** 使用[使用 PowerShell 創建主機池](create-host-pools-powershell.md)，手動將缺少的元件添加到 VM 中。

**原因 2：** PowerShell DSC 能夠啟動和執行，但未能完成，因為它無法登錄到 Windows 虛擬桌面並獲取所需資訊。

**修復 2：** 確認以下清單中的專案。

- 確保帳戶沒有 MFA。
- 確認租戶名稱準確，並且租戶存在於 Windows 虛擬桌面中。
- 確認該帳戶至少具有 RDS 參與者許可權。

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>錯誤：身份驗證失敗，C 錯誤：\Windows\Temp_ScriptLog.log.log

**原因：** PowerShell DSC 能夠執行，但無法連接到 Windows 虛擬桌面。

**修復：** 確認以下清單中的專案。

- 使用 Windows 虛擬桌面服務手動註冊 VM。
- 用於連接到 Windows 虛擬桌面的確認帳戶對租戶具有創建主機池的許可權。
- 確認帳戶沒有 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虛擬桌面代理未向 Windows 虛擬桌面服務註冊

首次在工作階段主機 VM 上安裝 Windows 虛擬桌面代理（手動或通過 Azure 資源管理器範本和 PowerShell DSC）時，它提供註冊權杖。 以下部分介紹適用于 Windows 虛擬桌面代理和權杖的疑難排解問題。

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>錯誤：在 Get-RdsSessionHost Cmdlet 中提交的狀態顯示狀態為"不可用"

![獲取 RdsSessionHost Cmdlet 將顯示狀態為"不可用"。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理無法將自己更新到新版本。

**修復：** 按照這些說明手動更新代理。

1. 在工作階段主機 VM 上下載代理的新版本。
2. 啟動工作管理員，並在"服務"選項卡中停止 RDAgentBootLoader 服務。
3. 運行新版本 Windows 虛擬桌面代理的安裝程式。
4. 當提示註冊權杖時，刪除INVALID_TOKEN條目，然後按下一個條目（不需要新權杖）。
5. 完成安裝精靈。
6. 打開工作管理員並啟動 RDAgentBootLoader 服務。

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>錯誤：Windows 虛擬桌面代理登錄機碼"註冊"顯示值 0

**原因：** 註冊權杖已過期或已生成過期值為 999999。

**修復：** 按照這些說明修復代理註冊表錯誤。

1. 如果已有註冊權杖，請使用刪除 RDS 註冊資訊將其刪除。
2. 使用 Rds-New註冊資訊生成新權杖。
3. 確認 -ExpriationHours 參數設置為 72（最大值為 99999）。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>錯誤：運行 Get-RdsSessionHost 時，Windows 虛擬桌面代理未報告活動訊號

**原因 1：** RDAgentBootLoader 服務已停止。

**修復 1：** 啟動工作管理員，如果服務選項卡報告 RDAgentBootLoader 服務的已停止狀態，則啟動該服務。

**原因 2：** 埠 443 可能關閉。

**修復 2：** 按照這些說明打開端口 443。

1. 通過從[Sys內部工具](/sysinternals/downloads/psping/)下載 PSPing 工具，確認埠 443 已打開。
2. 在代理正在運行的工作階段主機 VM 上安裝 PSPing。
3. 以管理員身份打開命令提示併發出以下命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 確認 PSPing 從 RD Broker 收到的資訊：

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>解決 Windows 虛擬桌面並排堆疊的問題

Windows 虛擬桌面並行堆疊會自動與 Windows 伺服器 2019 一起安裝。 使用 Microsoft 安裝程式 （MSI） 在 Microsoft Windows 伺服器 2016 或 Windows 伺服器 2012 R2 上安裝並行堆疊。 對於微軟 Windows 10，Windows 虛擬桌面並行堆疊啟用了**enablesxstacks.ps1**。

在工作階段主機池 VM 上安裝或啟用並行堆疊有三種主要方式：

- 使用 Azure 資源管理器**創建和預配新的 Windows 虛擬桌面主機池**範本
- 通過在主映射中包含並啟用
- 在每個 VM 上手動安裝或啟用（或使用擴展/電源外殼）

如果 Windows 虛擬桌面並排堆疊出現問題，請從命令提示符鍵入**qwinsta**命令，以確認已安裝或啟用並行堆疊。

如果安裝並啟用了並排堆疊，**則 qwinsta**的輸出將在輸出中列出**rdp-sxs。**

![並行堆疊安裝或啟用，輸出中 qwinsta 列為 rdp-sxs。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

檢查下面列出的登錄機碼，並確認其值是否匹配。 如果登錄機碼丟失或值不匹配，請按照創建[具有 PowerShell 的主機池](create-host-pools-powershell.md)中的說明來瞭解如何重新安裝並排堆疊。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>錯誤： O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE錯誤代碼。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 工作階段主機 VM 上未安裝並行堆疊。

**修復：** 按照這些說明在工作階段主機 VM 上安裝並行堆疊。

1. 使用遠端桌面協定 （RDP） 以本地管理員身份直接進入工作階段主機 VM。
2. 下載並導入[Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以便如果您尚未在 PowerShell 會話中使用，然後運行此 Cmdlet 以登錄您的帳戶：

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. 使用[PowerShell 創建主機池](create-host-pools-powershell.md)安裝並排堆疊。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修復出現故障的 Windows 虛擬桌面並排堆疊

已知情況可能導致並排堆疊出現故障：

- 未按照步驟的正確順序啟用並行堆疊
- 自動更新至 Windows 10 增強型多功能光碟 （EVD）
- 缺少遠端桌面工作階段主機 （RDSH） 角色
- 多次運行啟用 xsstackrc.ps1
- 在沒有本地管理員許可權的帳戶中運行 enablesxsstackrc.ps1

本節中的說明可以説明您卸載並行安裝 Windows 虛擬桌面堆疊。 卸載並排堆疊後，轉到[使用 PowerShell 創建主機池](create-host-pools-powershell.md)的"使用 Windows 虛擬桌面主機池註冊 VM"以重新安裝並排堆疊。

用於運行修正的 VM 必須與並排堆疊發生故障的 VM 位於同一子網和域上。

按照以下說明從同一子網和域運行修正：

1. 使用標準遠端桌面協定 （RDP） 連接到將應用修復程式的 VM。
2. 從 下載https://docs.microsoft.com/sysinternals/downloads/psexecPsExec。
3. 解壓縮下載的檔。
4. 以本地管理員身份啟動命令提示。
5. 導航到 PsExec 已解壓縮的資料夾。
6. 從命令提示符，使用以下命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname 是 VM 的機名，並排堆疊出現故障。

7. 按一下"同意"接受 PsExec 授權合約。

    ![軟體授權合約螢幕截圖。](media/SoftwareLicenseTerms.png)

    >[!Note]
    >此對話方塊將僅在首次運行 PsExec 時顯示。

8. 在 VM 上打開具有故障的並排堆疊後，運行 qwinsta 並確認名為 rdp-sxs 的條目可用。 如果沒有，VM 上不存在並排堆疊，因此問題不會與並行堆疊相關聯。

    ![管理員命令提示](media/AdministratorCommandPrompt.png)

9. 運行以下命令，該命令將列出安裝在 VM 上的 Microsoft 元件，並附帶故障的並排堆疊。

    ```cmd
        wmic product get name
    ```

10. 運行下面的命令，從上面的步驟中使用產品名稱。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸載以"遠端桌面"開頭的所有產品。

12. 卸載所有 Windows 虛擬桌面元件後，請按照作業系統的說明操作：

13. 如果作業系統是 Windows Server，請重新開機並排堆疊出現故障的 VM（使用 Azure 門戶或從 PsExec 工具）。

如果您的作業系統是 Microsoft Windows 10，請繼續執行以下說明：

14. 從運行 PsExec 的 VM、打開的檔資源管理器和複製禁用sxsstackrc.ps1到 VM 的系統磁碟機，並附帶故障的堆疊。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是 VM 的機名，並排堆疊出現故障。

15. 建議的過程：從 PsExec 工具啟動 PowerShell 並從上一步導航到資料夾，並運行禁用 ssstackrc.ps1。 或者，您可以運行以下 Cmdlet：

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 運行完 Cmdlet 後，使用故障並排堆疊重新開機 VM。

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>未配置遠端桌面許可模式

如果您使用管理帳戶登錄到 Windows 10 企業多會話，您可能會收到一條通知，指出"遠端桌面許可模式未配置，遠端桌面服務將在 X 天內停止工作。 在連接代理伺服器上，使用伺服器管理員指定遠端桌面許可模式。

如果時間限制到期，將顯示一條錯誤訊息，指出"遠端會話已中斷連線，因為此電腦沒有可用的遠端桌面用戶端存取許可證。

如果您看到這些消息中的任何一條，這意味著映射沒有安裝最新的 Windows 更新，或者您正在通過群組原則設置遠端桌面許可模式。 按照以下各節中的步驟檢查群組原則設置、標識 Windows 10 企業多會話的版本以及安裝相應的更新。  

>[!NOTE]
>當主機池包含 Windows 伺服器工作階段主機時，Windows 虛擬桌面僅需要 RDS 用戶端存取許可證 （CAL）。 要瞭解如何配置 RDS CAL，請參閱[使用用戶端存取許可證許可 RDS 部署](/windows-server/remote/remote-desktop-services/rds-client-access-license/)。

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>禁用遠端桌面許可模式群組原則設置

通過在 VM 中打開群組原則編輯器並導航到**管理範本** > **Windows 元件** > **遠端桌面服務遠端** > **桌面工作階段主機** > **許可** > **設置遠端桌面許可模式**，檢查群組原則設置。 如果群組原則設置為 **"已啟用"，** 則將其更改為 **"已禁用**"。 如果已禁用，則保留其正當時狀態。

>[!NOTE]
>如果通過域設置群組原則，請禁用針對這些 Windows 10 企業多會話 VM 的策略上的此設置。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>標識您使用的 Windows 10 企業多會話的版本

要檢查哪個版本的 Windows 10 企業多會話，請進行：

1. 使用管理員帳戶登錄。
2. 在"開始"功能表旁邊的搜索欄中輸入"關於"。
3. 選擇 **"關於你的電腦**"。
4. 檢查"版本"旁邊的數位。 數位應為"1809"或"1903"，如下圖所示。

    ![Windows 規範視窗的螢幕截圖。 版本號以藍色突出顯示。](media/windows-specifications.png)

現在，您已經瞭解了版本號，請跳到相關部分。

### <a name="version-1809"></a>版本 1809

如果您的版本號顯示"1809"，請安裝[KB4516077 更新](https://support.microsoft.com/help/4516077)。

### <a name="version-1903"></a>版本 1903

使用最新版本的 Windows 10 版本 1903 映射從 Azure 庫重新部署主機作業系統。

## <a name="next-steps"></a>後續步驟

- 有關 Windows 虛擬桌面和升級跟蹤的故障排除概述，請參閱[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)。
- 要在 Windows 虛擬桌面環境中創建租戶和主機池時解決問題，請參閱[租戶和主機池創建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虛擬桌面中配置虛擬機器 （VM） 時解決問題，請參閱[工作階段主機虛擬機器配置](troubleshoot-vm-configuration.md)。
- 要解決 Windows 虛擬桌面用戶端連接的問題，請參閱[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 要解決遠端桌面用戶端的問題，請參閱[排除遠端桌面用戶端的故障](troubleshoot-client.md)
- 要解決在 Windows 虛擬桌面中使用 PowerShell 時的問題，請參閱[Windows 虛擬桌面電源外殼](troubleshoot-powershell.md)。
- 要瞭解有關該服務的更多資訊，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
- 要完成疑難排解教程，請參閱[教程：解決資源管理器範本部署的疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../azure-resource-manager/templates/deployment-history.md)。
