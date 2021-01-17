---
title: 針對 Windows 虛擬桌面工作階段主機進行疑難排解-Azure
description: 如何在設定 Windows 虛擬桌面工作階段主機虛擬機器時解決問題。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84a49ea2f6ce4a5119af024ab3de67fa2b89c02e
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539738"
---
# <a name="session-host-virtual-machine-configuration"></a>設定工作階段主機虛擬機器

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md)。

您可以使用本文來針對 (Vm) 設定 Windows 虛擬桌面工作階段主機虛擬機器時所遇到的問題進行疑難排解。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="vms-are-not-joined-to-the-domain"></a>Vm 未加入網域

如果您在將虛擬機器 (Vm) 加入網域時遇到問題，請遵循下列指示。

- 使用將 [Windows Server 虛擬機器加入受控網域](../active-directory-domain-services/join-windows-vm.md) 或使用加入 [網域範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)中的程式，手動加入 VM。
- 嘗試從 VM 上的命令列 ping 功能變數名稱。
- 請參閱 [疑難排解網域加入錯誤訊息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)中的網域加入錯誤訊息清單。

### <a name="error-incorrect-credentials"></a>錯誤：不正確的認證

**原因：** 在 Azure Resource Manager 範本介面修正中輸入認證時，發生了打字錯誤。

**修正：** 採取下列其中一個動作來解決。

- 手動將 Vm 新增至網域。
- 確認認證之後，重新部署範本。 請參閱 [使用 PowerShell 建立主機集](create-host-pools-powershell.md)區。
- 使用範本將 [現有的 WINDOWS VM 加入 AD 網域](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)，將 vm 加入網域。

### <a name="error-timeout-waiting-for-user-input"></a>錯誤：等候使用者輸入時發生超時

**原因：** 用來完成加入網域的帳戶可能會有 (MFA) 的多重要素驗證。

**修正：** 採取下列其中一個動作來解決。

- 暫時移除帳戶的 MFA。
- 使用服務帳戶。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>錯誤：布建期間使用的帳戶沒有完成作業的許可權

**原因：** 基於合規性和法規，使用的帳戶沒有將 Vm 加入網域的許可權。

**修正：** 採取下列其中一個動作來解決。

- 使用屬於系統管理員群組成員的帳戶。
- 將必要的許可權授與所使用的帳戶。

### <a name="error-domain-name-doesnt-resolve"></a>錯誤：無法解析功能變數名稱

**原因1：** Vm 位於與網域所在的虛擬網路 (VNET) 無關的虛擬網路上。

**修正1：** 在布建 Vm 的 VNET 和執行網域控制站 (DC) 的 vnet 之間，建立 VNET 對等互連。 請參閱 [建立虛擬網路對等互連-Resource Manager、不同](../virtual-network/create-peering-different-subscriptions.md)的訂用帳戶。

**原因2：** 使用 Azure Active Directory Domain Services (Azure AD DS) 時，虛擬網路的 DNS 伺服器設定不會更新為指向受管理的網域控制站。

**修正2：** 若要更新包含 Azure AD DS 之虛擬網路的 DNS 設定，請參閱 [更新 Azure 虛擬網路的 dns 設定](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)。

**原因3：** 網路介面的 DNS 伺服器設定沒有指向虛擬網路上適當的 DNS 伺服器。

**修正3：** 遵循 [變更 DNS 伺服器] 中的步驟，採取下列其中一個動作來解決問題。
- 使用 [變更 dns 伺服器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)的步驟，將網路介面的 DNS 伺服器設定變更為 **自訂**，並指定虛擬網路上 DNS 伺服器的私人 IP 位址。
- 變更網路介面的 DNS 伺服器設定，以從 [[變更 dns 伺服器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)] 的步驟 **繼承虛擬網路**，然後變更虛擬網路的 dns 伺服器設定，以及變更 [dns 伺服器](../virtual-network/manage-virtual-network.md#change-dns-servers)的步驟。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>未安裝 windows 虛擬桌面代理程式和 Windows 虛擬桌面開機載入器

布建 Vm 的建議方式是使用 Azure 入口網站建立範本。 此範本會自動安裝 Windows 虛擬桌面代理程式和 Windows 虛擬桌面代理程式啟動載入器。

遵循這些指示來確認已安裝元件，並檢查錯誤訊息。

1. 簽入 **主控台**  >  **程式** 的  >  **程式和功能**，確認已安裝這兩個元件。 如果看不到 **Windows 虛擬桌面代理程式** 和 **Windows 虛擬桌面代理程式開機載入** 器，則它們不會安裝在 VM 上。
2. 開啟 **檔案總管** 並流覽至 **C:\Windows\Temp\ScriptLog.log**。 如果遺失該檔案，則表示安裝這兩個元件的 PowerShell DSC 無法在提供的安全性內容中執行。
3. 如果檔案 **C:\Windows\Temp\ScriptLog.log** 存在，請將它開啟，並檢查錯誤訊息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>錯誤：缺少 Windows 虛擬桌面代理程式和 Windows 虛擬桌面代理程式開機載入器。 也遺漏了 C:\Windows\Temp\ScriptLog.log

**原因1：** Azure Resource Manager 範本的輸入期間提供的認證不正確或許可權不足。

**修正1：** 使用 [PowerShell 建立主機集](create-host-pools-powershell.md)區，手動將遺漏的元件新增至 vm。

**原因2：** PowerShell DSC 可以啟動並執行，但無法完成，因為它無法登入 Windows 虛擬桌面並取得所需的資訊。

**修正2：** 確認下列清單中的專案。

- 請確定帳戶沒有 MFA。
- 請確認主機集區的名稱是否正確，以及主機集區是否存在於 Windows 虛擬桌面。
- 確認帳戶至少具有 Azure 訂用帳戶或資源群組的參與者許可權。

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>錯誤：驗證失敗，C:\Windows\Temp\ScriptLog.log 發生錯誤

**原因：** PowerShell DSC 可以執行，但無法連線至 Windows 虛擬桌面。

**修正：** 確認下列清單中的專案。

- 使用 Windows 虛擬桌面服務手動註冊 Vm。
- 確認用來連線到 Windows 虛擬桌面的帳戶具有 Azure 訂用帳戶或資源群組的許可權，才能建立主機集區。
- 確認帳戶沒有 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虛擬桌面代理程式未向 Windows 虛擬桌面服務註冊

當 Windows 虛擬桌面代理程式首次安裝于工作階段主機 Vm 時 (手動或透過 Azure Resource Manager 範本和 PowerShell DSC) ，它會提供註冊權杖。 下一節涵蓋適用于 Windows 虛擬桌面代理程式和權杖的疑難排解問題。

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>錯誤： Get-AzWvdSessionHost Cmdlet 中所記載的狀態會顯示為無法使用的狀態

> [!div class="mx-imgBorder"]
> ![AzWvdSessionHost 指令 Cmdlet 會將狀態顯示為無法使用。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理程式無法將自己更新為新版本。

**修正：** 請依照這些指示手動更新代理程式。

1. 在工作階段主機 VM 上下載新版的代理程式。
2. 啟動工作管理員，然後在 [服務] 索引標籤中，停止 RDAgentBootLoader 服務。
3. 針對新版本的 Windows 虛擬桌面代理程式執行安裝程式。
4. 當系統提示您輸入註冊權杖時，請移除 INVALID_TOKEN 的專案，然後按 [下一步] () 不需要新的權杖。
5. 完成安裝精靈。
6. 開啟工作管理員並啟動 RDAgentBootLoader 服務。

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>錯誤： Windows 虛擬桌面代理程式登錄專案 IsRegistered 顯示值為0

**原因：** 註冊權杖已過期。

**修正：** 請遵循下列指示來修正代理程式登錄錯誤。

1. 如果已經有註冊權杖，請使用 AzWvdRegistrationInfo 移除該權杖。
2. 執行 **AzWvdRegistrationInfo** Cmdlet 以產生新的權杖。
3. 確認 *-ExpriationTime* 參數設定為3天。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>錯誤：執行 Get-AzWvdSessionHost 時，Windows 虛擬桌面代理程式未回報心跳

**原因1：** RDAgentBootLoader 服務已停止。

**修正1：** 啟動工作管理員如果 [服務] 索引標籤回報 RDAgentBootLoader 服務的停止狀態，請啟動服務。

**原因2：** 埠443可能已關閉。

**修正2：** 遵循這些指示來開啟埠443。

1. 從 [Sysinternal 工具](/sysinternals/downloads/psping/)下載 PSPing 工具，確認埠443已開啟。
2. 在代理程式執行所在的工作階段主機 VM 上安裝 PSPing。
3. 以系統管理員身分開啟命令提示字元，併發出下列命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 確認 PSPing 從 RDBroker 收到資訊：

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>針對 Windows 虛擬桌面並存堆疊的問題進行疑難排解

Windows 虛擬桌面並存堆疊會隨 Windows Server 2019 自動安裝。 使用 Microsoft Installer (MSI) ，在 Microsoft Windows Server 2016 或 Windows Server 2012 R2 上安裝並存堆疊。 若為 Microsoft Windows 10，則會使用 **enablesxstackrs.ps1** 啟用 Windows 虛擬桌面並存堆疊。

有三種主要方式可在工作階段主機集區 Vm 上安裝或啟用並存堆疊：

- 使用 Azure 入口網站建立範本
- 藉由在主要映射上包含並啟用
- 在每個 VM 上手動安裝或啟用 (或使用擴充功能/PowerShell) 

如果您的 Windows 虛擬桌面並存堆疊發生問題，請從命令提示字元輸入 **qwinsta** 命令，確認已安裝或啟用並存堆疊。

如果已安裝且啟用並存堆疊，則 **qwinsta** 的輸出會在輸出中列出 **rdp sxs** 。

> [!div class="mx-imgBorder"]
> ![在輸出中列為 rdp sxs 的 qwinsta，安裝或啟用並存堆疊。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

檢查下列登錄專案，並確認它們的值相符。 如果登錄機碼遺失或值不相符，請依照 [使用 PowerShell 建立主機集](create-host-pools-powershell.md) 區中的指示，瞭解如何重新安裝並存堆疊。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>錯誤： O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE 錯誤碼。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 工作階段主機 VM 上未安裝並存堆疊。

**修正：** 請遵循下列指示，在工作階段主機 VM 上安裝並存堆疊。

1. 使用遠端桌面通訊協定 (RDP) 以本機系統管理員身分直接進入工作階段主機 VM。
2. 使用「使用 [PowerShell 建立主機集](create-host-pools-powershell.md)區」來安裝並存堆疊。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修正故障的 Windows 虛擬桌面並存堆疊

有一些已知的情況可能會導致並存堆疊故障：

- 未遵循正確的步驟順序來啟用並存堆疊
- 自動更新 Windows 10 增強的多功能光碟 (EVD) 
- 缺少遠端桌面工作階段主機 (RDSH) 角色
- 執行 enablesxsstackrc.ps1 多次
- 在沒有本機系統管理員許可權的帳戶中執行 enablesxsstackrc.ps1

本節中的指示可協助您卸載 Windows 虛擬桌面並存堆疊。 卸載並存堆疊後，請移至 [使用 PowerShell 建立主機集](create-host-pools-powershell.md) 區中的「向 Windows 虛擬桌面主機集區註冊 VM」，以重新安裝並存堆疊。

用來執行補救的 VM 必須位於與 VM 位於相同的子網和網域，且具有故障的並存堆疊。

請遵循下列指示，從相同的子網和網域執行補救：

1. 使用標準遠端桌面通訊協定 (RDP) 連接到將套用修正的 VM。
2. 從下載 PsExec https://docs.microsoft.com/sysinternals/downloads/psexec 。
3. 將下載的檔案解壓縮。
4. 以本機系統管理員身分啟動命令提示字元。
5. 流覽至已解壓縮 PsExec 的資料夾。
6. 從命令提示字元中，使用下列命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname 是具有故障並存堆疊的 VM 電腦名稱稱。

7. 按一下 [同意] 接受 PsExec 授權合約。

    > [!div class="mx-imgBorder"]
    > ![軟體授權合約螢幕擷取畫面。](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >只有在第一次執行 PsExec 時，才會顯示此對話方塊。

8. 在具有故障並存堆疊的 VM 上開啟命令提示字元會話之後，請執行 qwinsta，並確認有一個名為 rdp sxs 的專案可供使用。 如果沒有，VM 上就不會有並存堆疊，因此問題不會系結至並存堆疊。

    > [!div class="mx-imgBorder"]
    > ![系統管理員命令提示字元](media/AdministratorCommandPrompt.png)

9. 執行下列命令，此命令會列出在 VM 上安裝的 Microsoft 元件，並具有故障的並存堆疊。

    ```cmd
        wmic product get name
    ```

10. 使用上述步驟中的產品名稱執行下列命令。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸載以「遠端桌面」開頭的所有產品。

12. 卸載所有 Windows 虛擬桌面元件之後，請遵循適用于您作業系統的指示：

13. 如果您的作業系統是 Windows Server，請重新開機具有故障之並存堆疊的 VM (使用 Azure 入口網站或從 PsExec 工具) 。

如果您的作業系統是 Microsoft Windows 10，請繼續執行下列指示：

14. 從執行 PsExec 的 VM 中，開啟檔案總管，並將 disablesxsstackrc.ps1 複製到 VM 的系統磁片磁碟機，且該 VM 的並行堆疊不正常。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是具有故障並存堆疊的 VM 電腦名稱稱。

15. 建議的進程：從 PsExec 工具啟動 PowerShell，並流覽至上一個步驟中的資料夾，然後執行 disablesxsstackrc.ps1。 或者，您可以執行下列 Cmdlet：

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 當 Cmdlet 執行完成時，請以並存堆疊的故障重新開機 VM。

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>未設定遠端桌面授權模式

如果您使用系統管理帳戶登入 Windows 10 企業版多會話，您可能會收到通知，指出「未設定遠端桌面授權模式，遠端桌面服務將于 X 天內停止運作。 在連接代理程式伺服器上，使用伺服器管理員指定遠端桌面授權模式。」

如果時間限制過期，則會出現一則錯誤訊息，指出「遠端會話已中斷連線，因為這部電腦沒有可用的遠端桌面用戶端存取授權」。

如果您看到上述任一訊息，表示映射未安裝最新的 Windows 更新，或您正在透過群組原則設定遠端桌面授權模式。 遵循下一節中的步驟來檢查群組原則設定、識別 Windows 10 企業版多重會話的版本，並安裝對應的更新。

>[!NOTE]
>當您的主機集區包含 Windows Server 工作階段主機時，Windows 虛擬桌面只需要 (CAL) RDS 用戶端存取許可證。 若要瞭解如何設定 RDS CAL，請參閱 [使用用戶端存取授權授權 RDS 部署](/windows-server/remote/remote-desktop-services/rds-client-access-license/)。

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>停用遠端桌面授權模式群組原則設定

在 VM 中開啟群組原則編輯器，並流覽至 **系統管理範本**  >  **Windows 元件**  >  **遠端桌面服務**  >  **遠端桌面工作階段主機**  >  **授權**  >  **設定遠端桌面授權模式**，以檢查群組原則設定。 如果 **已啟用** 群組原則設定，請將其變更為 [ **已停用**]。 如果已停用，則保持原狀。

>[!NOTE]
>如果您透過網域設定群組原則，請在以這些 Windows 10 企業版多重會話 Vm 為目標的原則上停用此設定。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>找出您所使用的 Windows 10 企業版多個會話版本

若要檢查您擁有的 Windows 10 企業版多個會話版本：

1. 使用您的系統管理員帳戶登入。
2. 在 [開始] 功能表旁的搜尋列中輸入 "About"。
3. 選取 [ **關於您的電腦**]。
4. 檢查 "Version" 旁的數位。 此數位應該是 "1809" 或 "1903"，如下圖所示。

    > [!div class="mx-imgBorder"]
    > ![[Windows 規格] 視窗的螢幕擷取畫面。 版本號碼會以藍色反白顯示。](media/windows-specifications.png)

既然您已經知道您的版本號碼，請直接跳到相關章節。

### <a name="version-1809"></a>版本1809

如果您的版本號碼顯示為 "1809"，請安裝 [KB4516077 更新](https://support.microsoft.com/help/4516077)。

### <a name="version-1903"></a>版本1903

從 Azure 資源庫使用最新版的 Windows 10 版本1903映射重新部署主機作業系統。

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>因為發生安全性錯誤，所以無法連線到遠端電腦

如果您的使用者看到錯誤：「無法連線到遠端電腦，因為發生安全性錯誤。 如果發生這種情況，請要求您的系統管理員或技術支援「驗證任何現有的原則，以變更預設的 RDP 許可權。 可能導致此錯誤出現的一個原則是「允許透過遠端桌面服務安全性原則登入」。

若要深入瞭解此原則，請參閱 [允許登入遠端桌面服務](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立主機集區時所發生的問題進行疑難排解，請參閱 [環境和主機集區建立](troubleshoot-set-up-issues.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要針對與 Windows 虛擬桌面代理程式或會話連線相關的問題進行疑難排解，請參閱針對 [常見的 Windows 虛擬桌面代理程式問題進行疑難排解](troubleshoot-agent.md)。
- 若要對 Windows 虛擬桌面用戶端連線問題進行疑難排解，請參閱 [Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱 [遠端桌面用戶端疑難排解](troubleshoot-client.md)
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解此服務，請參閱 [Windows 虛擬桌面環境](environment-setup.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../azure-resource-manager/templates/deployment-history.md)。
