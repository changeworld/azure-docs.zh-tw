---
title: 使用 Azure Site Recovery 管理損毀修復的設定伺服器
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: bcd232a3242b0341bfc81fa9785f76b0d3bd90cb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369449"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>管理 VMware VM/實體伺服器災害復原的設定伺服器

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會設定內部部署設定伺服器。 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊，以及管理資料複寫。 本文摘要說明部署設定伺服器之後，管理設定伺服器的一般工作。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>更新 Windows 授權

使用 OVF 範本所提供的授權是有效期 180 天的評估授權。 如需不中斷的使用權限，您必須使用購買的授權來啟用 Windows。 授權更新可以透過獨立金鑰或 KMS 標準金鑰來完成。 您可以在 [執行作業系統的 DISM Windows 命令列取得](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options)指導方針。 若要取得金鑰，請參閱 [設定的 KMS 用戶端](/windows-server/get-started/kmsclientkeys)。

## <a name="access-configuration-server"></a>存取設定伺服器

您可以依照下列方式存取組態伺服器：

* 登入部署所在的 VM，然後從桌面捷徑啟動 **Azure Site Recovery 設定管理員**。
* 或者，您也可以從 HTTPs://*>configurationservername*/： 44315/遠端存取設定伺服器。 使用系統管理員認證登入。

## <a name="modify-vmware-server-settings"></a>修改 VMware 伺服器設定

1. 若要為不同的 VMware 伺服器與設定伺服器建立關聯，請在[登入](#access-configuration-server)後選取 [新增 vCenter Server/vSphere ESXi 伺服器]****。
2. 輸入詳細資料，然後選取 [確定]****。

## <a name="modify-credentials-for-automatic-discovery"></a>修改自動探索所需的認證

1. 若要更新用來連線到 VMware 伺服器以自動探索 VMware VM 的認證，請在[登入](#access-configuration-server)之後選擇該帳戶，然後按一下 [編輯]****。
2. 輸入新的認證，然後選取 [確定] ****。

    ![修改 VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

您也可以透過 CSPSConfigtool.exe 來修改認證。

1. 登入設定伺服器並啟動 CSPSConfigtool.exe
2. 選擇您要修改的帳戶類型，然後按一下 [編輯]****。
3. 輸入已修改的認證，然後按一下 [確定]****

## <a name="modify-credentials-for-mobility-service-installation"></a>修改行動服務安裝的認證

針對您啟用複寫的 VMware VM，修改在其上自動安裝行動服務所用的認證。

1. 登[入](#access-configuration-server)之後，選取 [**管理虛擬機器認證**]
2. 選擇您想要修改的帳戶，然後按一下 [**編輯**]。
3. 輸入新的認證，然後選取 [確定] ****。

    ![修改行動服務認證](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

您也可以透過 CSPSConfigtool.exe 來修改認證。

1. 登入設定伺服器並啟動 CSPSConfigtool.exe
2. 選擇您想要修改的帳戶，然後按一下 [**編輯**]。
3. 輸入新的認證，然後按一下 [確定]****。

## <a name="add-credentials-for-mobility-service-installation"></a>新增行動服務安裝的認證

若未在設定伺服器的 OVF 部署期間新增認證，

1. 在[登入](#access-configuration-server)之後，選取 [管理虛擬機器認證]****。
2. 按一下 [新增虛擬機器認證]****。
    ![螢幕擷取畫面顯示 [管理虛擬機器認證] 窗格，其中包含 [新增虛擬機器認證] 連結。](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. 輸入新的認證，然後按一下 [新增]****。

您也可以透過 CSPSConfigtool.exe 來新增認證。

1. 登入設定伺服器並啟動 CSPSConfigtool.exe
2. 按一下 [新增]****，輸入新的認證，然後按一下 [確定]****。

## <a name="modify-proxy-settings"></a>修改 Proxy 設定

修改設定伺服器電腦用來透過網際網路存取 Azure 的 Proxy 設定。 如果您除了在設定伺服器電腦上執行的預設處理伺服器之外還有處理伺服器電腦，則請修改兩部電腦上的設定。

1. 在[登入](#access-configuration-server)設定伺服器之後，選取 [管理連線]****。
2. 更新 Proxy 值。 然後選取 [儲存]**** 以更新設定。

## <a name="add-a-network-adapter"></a>新增網路介面卡

開放式虛擬化格式 (OVF) 範本會部署包含單一網路介面卡的設定伺服器 VM。

- 您可以 [將其他介面卡新增至 VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)，但必須在保存庫中註冊設定伺服器之前新增。
- 若要在保存庫中註冊設定伺服器之後新增介面卡，請在 VM 屬性中新增介面卡。 接著，您必須在保存庫中[重新註冊](#reregister-a-configuration-server-in-the-same-vault)伺服器。

## <a name="how-to-renew-ssl-certificates"></a>如何更新 SSL 憑證

設定伺服器有內建的 web 伺服器，可協調所有受保護電腦上的行動代理程式活動、內建/相應放大進程伺服器，以及連接到它的主要目標伺服器。 Web 伺服器使用 SSL 憑證來驗證用戶端。 憑證會在三年後到期，且可隨時更新。

### <a name="check-expiry"></a>檢查到期日

到期日顯示在 [設定伺服器健康情況]**** 下。 針對在 2016 年 5 月之前部署的設定伺服器，憑證到期設定為一年。 如果您有即將到期的憑證，會發生以下狀況：

- 當到期日是兩個月或少於兩個月時，服務會開始在入口網站傳送通知，以及透過電子郵件傳送通知 (如果您已訂閱 Site Recovery 通知)。
- 通知橫幅會出現在保存庫資源頁面上。 如需詳細資訊，請選取橫幅。
- 如果您看到 [立即升級]**** 按鈕，表示您的環境中有些元件尚未升級至 9.4.xxxx.x 或更新版本。 更新憑證之前請先升級元件。 您不能在舊版上更新。

### <a name="if-certificates-are-yet-to-expire"></a>如果憑證尚未過期

1. 若要更新，請在保存庫中開啟**Site Recovery 基礎結構**設定  >  **伺服器**。 選取必要的設定伺服器。
2. 確定所有受保護電腦上的所有元件相應放大進程伺服器、主要目標伺服器和行動代理程式都是在最新版本，而且處於線上狀態。
3. 現在，選取 [ **更新憑證**]。
4. 請仔細遵循此頁面上的指示，然後按一下 [確定] 以更新所選設定伺服器及其相關聯元件的憑證。

### <a name="if-certificates-have-already-expired"></a>如果憑證已過期

1. 到期後，即 **無法從 Azure 入口網站更新**憑證。 繼續之前，請確定所有受保護電腦上的所有元件相應放大進程伺服器、主要目標伺服器和行動代理程式都是在最新版本上，而且處於線上狀態。
2. **只有在憑證已過期時，才遵循此程式。** 登入設定伺服器，流覽至 C 磁片磁碟機 > 程式資料 > Site Recovery > home > svsystems > bin，然後以系統管理員身分執行 "Renewcerts.exe" 執行程式工具。
3. PowerShell 執行視窗會彈出並觸發憑證更新。 這項作業可能需要 15 分鐘的時間。 在更新完成之前，請勿關閉視窗。

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>在同一個保存庫中註冊設定伺服器

如果需要，您可以在同一個保存庫中註冊設定伺服器。 如果您除了在設定伺服器電腦上執行的預設處理伺服器之外，還有其他處理伺服器電腦，則請註冊這兩部電腦。


1. 在保存庫中，開啟 [**管理**  >  **Site Recovery 基礎結構**設定  >  **伺服器**]。
2. 在 [伺服器]**** 中，選取 [下載註冊金鑰]**** 以下載保存庫認證檔案。
3. 登入設定伺服器電腦。
4. 在 **%ProgramData%\ASR\home\svsystems\bin** 中，開啟 **cspsconfigtool.exe**。
5. 在 [保存庫註冊]**** 索引標籤上，選取 [瀏覽]**** 並找出您下載的保存庫認證檔。
6. 如果需要，請提供 Proxy 伺服器詳細資料。 然後，選取 [註冊]。
7. 開啟系統管理 PowerShell 命令視窗並執行下列命令：
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >若要從設定伺服器 **提取最新的憑證** 來向外延展進程伺服器，請執行命令 *" \<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> "--registermt*

8. 最後，執行下列命令以重新啟動 obengine。
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>向不同保存庫註冊組態伺服器

> [!WARNING]
> 下列步驟會解除組態伺服器和目前保存庫之間的關聯，且組態伺服器底下所有受保護虛擬機器的複寫都會遭到停止。

1. 登入設定伺服器。
2. 開啟系統管理 PowerShell 命令視窗並執行下列命令：

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. 使用您桌面上的捷徑啟動設定伺服器設備瀏覽器。
4. 執行與新設定伺服器[註冊](vmware-azure-tutorial.md#register-the-configuration-server)類似的註冊步驟。

## <a name="upgrade-the-configuration-server"></a>升級設定伺服器

執行更新彙總來更新組態伺服器。 更新最多可以套用到 N-4 版本。 例如：

- 如果您執行 9.7、9.8、9.9 或 9.10，就可以直接升級至 9.11。
- 如果您執行 9.6 或更早版本，而且想要升級至 9.11，則在升級到 9.11 之前，必須先升級到 版本 9.7。

如需 Azure Site Recovery 元件支援聲明的詳細指引，請參閱[這裡](./service-updates-how-to.md#support-statement-for-azure-site-recovery)。
用來升級至組態伺服器所有版本的更新彙總連結，可以在[這裡](./service-updates-how-to.md#links-to-currently-supported-update-rollups)取得。

> [!IMPORTANT]
> 每當發行新的 Azure Site Recovery 元件版本 'N' 時，'N-4' 以下的所有版本就會被視為不支援的版本。 我們一律建議您升級到可用的最新版本。</br>
> 如需 Azure Site Recovery 元件支援聲明的詳細指引，請參閱[這裡](./service-updates-how-to.md#support-statement-for-azure-site-recovery)。

升級伺服器，如下所示：

1. 在保存庫中，移至 [**管理**  >  **Site Recovery 基礎結構**設定  >  **伺服器**]。
2. 如果有可用的更新，[代理程式版本]**** > [資料行] 中會出現連結。
    ![更新](./media/vmware-azure-manage-configuration-server/update2.png)
3. 將更新安裝程式檔案下載到組態伺服器上。

    ![更新](./media/vmware-azure-manage-configuration-server/update1.png)

4. 按兩下以執行安裝程式。
5. 安裝程式會偵測機器上執行的目前版本。 按一下 [是]**** 開始進行升級。
6. 升級完成時，會驗證伺服器組態。

    ![更新](./media/vmware-azure-manage-configuration-server/update3.png)

7. 按一下 [完成]**** 以關閉安裝程式。
8. 若要升級其餘 Site Recovery 元件，請參閱我們的[升級指引](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure)。

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>您可以從命令列升級組態伺服器/處理序伺服器

執行安裝檔案，如下所示：

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>範例用法
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>參數

|參數名稱| 類型 | 描述| 值|
|-|-|-|-|
| /ServerMode|必要|指定應該同時安裝組態和處理序伺服器，還是只安裝處理序伺服器|CS<br>PS|
|/InstallLocation|必要|安裝元件的資料夾| 電腦上的任何資料夾|
|/MySQLCredsFilePath|必要|儲存 MySQL 伺服器認證的檔案路徑|此檔案應該具備如下所指定的格式|
|/VaultCredsFilePath|必要|保存庫認證檔的路徑|有效的檔案路徑|
|/EnvType|必要|您想要保護的環境類型 |VMware<br>NonVMware|
|/PSIP|必要|要用於複寫資料傳輸的 NIC IP 位址| 任何有效的 IP 位址|
|/CSIP|必要|接聽組態伺服器的 NIC IP 位址| 任何有效的 IP 位址|
|/PassphraseFilePath|必要|複雜密碼檔案的位置完整路徑|有效的檔案路徑|
|/BypassProxy|選擇性|指定組態伺服器不使用 Proxy 連接至 Azure|若要這樣做，請從 Venu 取得此值|
|/ProxySettingsFilePath|選擇性|Proxy 設定 (預設的 Proxy 需要驗證或自訂的 Proxy)|此檔案應該具備如下所指定的格式|
|DataTransferSecurePort|選擇性|要用於複寫資料的 PSIP 上的連接埠號碼| 有效的連接埠號碼 (預設值是 9433)|
|/SkipSpaceCheck|選擇性|略過快取磁碟的空間檢查| |
|/AcceptThirdpartyEULA|必要|旗標表示接受協力廠商使用者授權合約| |
|/ShowThirdpartyEULA|選擇性|顯示協力廠商使用者授權合約。 如果提供作為輸入，則會忽略所有其他參數| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>建立 MYSQLCredsFilePath 的檔案輸入

MySQLCredsFilePath 參數使用檔案作為輸入。 使用下列格式建立檔案，並將它以輸入 MySQLCredsFilePath 參數傳遞。
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>建立 ProxySettingsFilePath 的檔案輸入
ProxySettingsFilePath 參數使用檔案做為輸入。 使用下列格式建立檔案，並將它以輸入 ProxySettingsFilePath 參數傳遞。

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>將設定伺服器刪除或取消註冊

1. 對組態伺服器下的所有 VM [停用保護](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
2. 將設定伺服器的所有複寫原則[解除關聯](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)並[刪除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)。
3. [刪除](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)與設定伺服器關聯的所有 vCenter 伺服器/vSphere 主機。
4. 在保存庫中，開啟**Site Recovery 基礎結構**設定  >  **伺服器**]。
5. 選取您想要移除的設定伺服器。 然後在 [詳細資料]**** 頁面上選取 [刪除]****。

    ![刪除設定伺服器](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>以 PowerShell 刪除

您可以選擇使用 PowerShell 來刪除設定伺服器。

1. [安裝](/powershell/azure/install-Az-ps) Azure PowerShell 模組。
2. 使用以下命令登入您的 Azure 帳戶：

    `Connect-AzAccount`
3. 選取保存庫訂用帳戶。

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  設定保存庫內容。

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 擷取設定伺服器。

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 刪除組態伺服器。

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> 您可以在 Remove-AzSiteRecoveryFabric 中使用 **-Force** 選項，以強制刪除設定伺服器。

## <a name="generate-configuration-server-passphrase"></a>產生設定伺服器複雜密碼

1. 登入組態伺服器，然後以系統管理員身分開啟命令提示字元視窗。
2. 若要將目錄切換至 bin 資料夾，執行命令 **cd %ProgramData%\ASR\home\svsystems\bin**
3. 若要產生複雜密碼檔案，請執行 **genpassphrase.exe -v > MobSvc.passphrase**。
4. 您的複雜密碼將會儲存在位於 **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase** 的這個檔案中。

## <a name="refresh-configuration-server"></a>重新整理組態伺服器

1. 在 Azure 入口網站中，流覽至 [復原**服務保存庫**]，以  >  **管理**  >  VMware & 實體機器設定**Site Recovery Infrastructure**  >  **For VMware & Physical machines**  >  **伺服器**的 Site Recovery 基礎結構
2. 按一下您想要重新整理的組態伺服器。
3. 在包含所選設定伺服器詳細資料的分頁上 **，按一下 [** 重新整理  >  **伺服器**]。
4. 監視復原**服務保存庫**  >  **監視**  >  **Site Recovery 工作**下的作業進度。

## <a name="failback-requirements"></a>容錯回復需求

在重新保護和容錯回復期間，內部部署組態伺服器必須會執行並處於連線連線狀態。 若要成功完成容錯回復，要容錯回復的虛擬機器必須存在於組態伺服器資料庫中。

請確定您定期排程備份組態伺服器。 如果發生災害，而且遺失組態伺服器，您必須先從備份副本還原組態伺服器，並確保還原的組態伺服器 IP 位址與註冊到保存庫的 IP 位址相同。 如果還原的組態伺服器使用不同 IP 位址，則容錯回復將無法運作。

## <a name="next-steps"></a>後續步驟

檢閱將 [VMware VM](vmware-azure-tutorial.md) 設定為災害復原至 Azure 的教學課程。