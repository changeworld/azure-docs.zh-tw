---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原期間的組態伺服器問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 進行 VMware VM 和實體伺服器至 Azure 的災害復原，提供部署組態伺服器的疑難排解資訊。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 26376c6b20816d2e7302403c8391195e16092fa3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85504315"
---
# <a name="troubleshoot-configuration-server-issues"></a>針對組態伺服器問題進行疑難排解

本文可協助您疑難排解在部署和管理 [Azure Site Recovery](site-recovery-overview.md) 組態伺服器時遭遇的問題。 組態伺服器可作為管理伺服器。 透過使用 Site Recovery，以使用組態伺服器來設定內部部署 VMware VM 和實體伺服器至 Azure 的災害復原。 下列各節將討論當您新增新組態伺服器時與管理組態伺服器時，可能會碰到的常見失敗情況。

## <a name="registration-failures"></a>註冊失敗

當您安裝行動代理程式時，來源電腦會向設定伺服器註冊。 您可以遵循下列的指導方針，對此步驟期間所發生的任何失敗偵錯：

1. 開啟 C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 檔案。 (ProgramData 資料夾可能是隱藏的資料夾。 如果您沒有看到 [ProgramData] 資料夾，請在 [檔案管理器] 的 [**視圖**] 索引標籤的 [**顯示/隱藏**] 區段中，選取 [**隱藏的專案**] 核取方塊。）失敗可能是由多個問題所造成。

2. 搜尋字串 **No Valid IP Address found**。 如果找到該字串：
   1. 請驗證要求的主機識別碼與來源電腦的主機識別碼相同。
   2. 確認來源電腦具有至少一個指派給實體 NIC 的 IP。 為了使來源電腦成功向設定伺服器進行代理程式註冊，來源電腦必須具有至少一個指派給實體 NIC 的有效 IP v4 位址。
   3. 在來源電腦上執行下列其中一個命令，以取得來源電腦的所有 IP 位址：
      - 對於 Windows：`> ipconfig /all`
      - 對於 Linux：`# ifconfig -a`

3. 如果找不到字串 **No Valid IP Address found**，則搜尋字串 **Reason=>NULL**。 如果來源電腦使用空白主機向設定伺服器註冊時，就會發生此錯誤。 如果找到該字串：
    - 在您解決問題後，請依照[向設定伺服器註冊來源電腦](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中所列的指導方針來手動重試註冊。

4. 如果找不到字串 **Reason=>NULL**，則在來源電腦上開啟 C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log 檔案。 (ProgramData 資料夾可能是隱藏的資料夾。 如果您沒有看到 [ProgramData] 資料夾，請在 [檔案管理器] 的 [**視圖**] 索引標籤的 [**顯示/隱藏**] 區段中，選取 [**隱藏的專案**] 核取方塊。）失敗可能是由多個問題所造成。 

5. 搜尋字串**post 要求：（7）-無法連接到伺服器**。 如果找到該字串：
    1. 解決來源電腦與設定伺服器之間的網路問題。 確認可透過使用如 ping、traceroute 或網頁瀏覽器等網路工具，從來源電腦連線到設定伺服器。 確認來源電腦可透過連接埠 443 連線到設定伺服器。
    2. 檢查來源電腦上是否有任何防火牆規則會封鎖來源電腦與設定伺服器之間的連線。 請與您的網路管理員合作來解除封鎖任何連線問題。
    3. 請確認[自防毒程式排除的 Site Recovery 資料夾](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)中所列的資料夾已自防毒軟體排除。
    4. 解決網路問題後，請依照[向設定伺服器註冊來源電腦](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指導方針重試註冊。

6. 如果找不**到字串 post 要求：（7）-無法連接到伺服器**，請在相同的記錄檔中尋找字串**要求：（60）-對等憑證無法使用指定的 CA 憑證進行驗證**。 發生此錯誤的原因可能是設定伺服器憑證已過期，或來源電腦不支援 TLS 1.0 或更新的通訊協定。 如果防火牆封鎖來源電腦與設定伺服器之間的 TLS 通訊，也可能會發生此問題。 如果找到該字串： 
    1. 若要解決，請在來源電腦上使用網頁瀏覽器連線至設定伺服器的 IP 位址。 使用 URI https:\/\/<設定伺服器 IP 位址\>:443/。 確認來源電腦可透過連接埠 443 連線到設定伺服器。
    2. 檢查來源電腦上是否必須新增或移除任何防火牆規則，來源電腦才能與設定伺服器通訊。 由於可能有各種防火牆軟體正在使用中，因此我們無法列出所有必要的防火牆設定。 請與您的網路管理員合作來解除封鎖任何連線問題。
    3. 請確認[自防毒程式排除的 Site Recovery 資料夾](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)中所列的資料夾已自防毒軟體排除。  
    4. 在您解決問題後，請依照[向設定伺服器註冊來源電腦](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指導方針重試註冊。

7. 在 Linux 上，如果 <INSTALLATION_DIR\>/etc/drscout.conf 中的平台值損毀，則註冊會失敗。 若要識別此問題，請開啟 /var/log/ua_install.log 檔案。 搜尋字串 **Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure**。 平台應設為 **VmWare** 或 **Azure**。 如果 drscout.conf 檔案已損毀，建議您[解除安裝行動代理程式](vmware-physical-manage-mobility-service.md#uninstall-mobility-service)，然後重新安裝行動代理程式。 如果卸載失敗，請完成下列步驟： a。 開啟 Installation_Directory/uninstall.sh 檔案，並為 **StopServices** 函式的呼叫加上註解。
    b. 開啟 Installation_Directory/Vx/bin/uninstall.sh 檔案，並為 **stop_services** 函式的呼叫加上註解。
    c. 開啟 Installation_Directory/Fx/uninstall.sh 檔案，並將嘗試停止 Fx 服務的整個區段加上註解。
    d. [卸載](vmware-physical-manage-mobility-service.md#uninstall-mobility-service)行動代理程式。 成功解除安裝之後，請將系統重新開機，然後嘗試重新安裝代理程式。

8. 請確定未針對使用者帳戶啟用多重要素驗證。 Azure Site Recovery 目前不支援使用者帳戶的多重要素驗證。 在未啟用多重要素驗證的使用者帳戶的情況下，註冊設定伺服器。  

## <a name="installation-failure-failed-to-load-accounts"></a>安裝失敗：無法載入帳戶

如果服務在安裝行動代理程式並向設定伺服器註冊時，無法從傳輸連線讀取資料，就會發生此錯誤。 若要解決此問題，請確定已在來源電腦上啟用 TLS 1.0。

## <a name="vcenter-discovery-failures"></a>vCenter 探索失敗

若要解決 vCenter 探索失敗，請將 vCenter 伺服器新增至 byPass 清單 Proxy 設定。 

- 請從[此處](https://aka.ms/PsExec)下載 PsExec 工具，以存取系統使用者內容。
- 藉由執行下列命令列   psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"，在系統使用者內容中開啟 Internet Explorer
- 在 IE 中新增 Proxy 設定，然後重新啟動 tmanssvc 服務。
- 若要設定 DRA Proxy 設定，請執行   cd C:\Program Files\Microsoft Azure Site Recovery Provider
- 接下來，執行 DRCONFIGURATOR.EXE /configure /AddBypassUrls [新增[組態伺服器部署](vmware-azure-deploy-configuration-server.md#configure-settings)的**設定 vCenter Server/vSphere ESXi 伺服器**步驟進行期間所提供的 IP 位址/FQDN]

## <a name="change-the-ip-address-of-the-configuration-server"></a>變更設定伺服器的 IP 位址

強烈建議不要變更設定伺服器的 IP 位址。 確定指派給設定伺服器的所有 IP 位址皆為靜態位址。 請勿使用 DHCP IP 位址。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008： SAML 權杖無效

若要避免這個錯誤，請確定系統時鐘上的時間與本地時間的差異未超過 15 分鐘。 重新執行安裝程式以完成註冊。

## <a name="failed-to-create-a-certificate"></a>無法建立憑證

無法建立驗證 Site Recovery 所需的憑證。 請先確定您是以本機系統管理員身分執行安裝程式，再重新執行安裝程式。

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>無法啟動從伺服器標準評估到伺服器標準的 Windows 授權

1. 在透過 OVF 進行設定伺服器部署的過程中，會使用評估授權，其有效期為180天。 您必須在此授權過期前啟用此授權。 否則，這可能會導致設定伺服器頻繁關機，因而導致阻礙複寫活動。
2. 如果您無法啟用 Windows 的授權，請連絡 [Windows 支援小組](https://aka.ms/Windows_Support)來解決此問題。

## <a name="register-source-machine-with-configuration-server"></a>向設定伺服器註冊來源電腦

### <a name="if-the-source-machine-runs-windows"></a>如果來源電腦執行 Windows

在來源電腦上執行下列命令：

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

設定 | 詳細資料
--- | ---
使用方式 | UnifiedAgentConfigurator.exe  /CSEndPoint <設定伺服器 IP位址\> /PassphraseFilePath <複雜密碼檔案路徑\>
代理程式設定記錄 | 位在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 底下。
/CSEndPoint | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
/PassphraseFilePath |  Mandatory。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。

### <a name="if-the-source-machine-runs-linux"></a>如果來源電腦執行 Linux

在來源電腦上執行下列命令：

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

設定 | 詳細資料
--- | ---
使用方式 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <設定伺服器 IP 位址\> -P <複雜密碼檔案路徑\>
-i | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
-P |  Mandatory。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾。

## <a name="unable-to-configure-the-configuration-server"></a>無法設定組態伺服器

如果您在虛擬機器上安裝組態伺服器以外的應用程式，您可能無法設定主要目標。 

組態伺服器必須是單一用途伺服器，不支援使用它作為共用伺服器。 

如需詳細資訊，請參閱[部署組態伺服器](vmware-azure-deploy-configuration-server.md#faqs)中的設定常見問題集。 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>從組態伺服器資料庫中移除受保護項目的過時項目 

若要移除組態伺服器上過時的受保護機器，使用下列步驟。 
 
1. 若要判斷過時項目的來源機器和 IP 位址： 

    1. 在系統管理員模式中開啟 MYSQL 命令列。 
    2. 執行下列命令。 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        這會傳回已註冊的機器清單，以及其 IP 位址和上次活動訊號。 尋找具有過時複寫配對的主機。

2. 開啟提升權限的命令提示字元並瀏覽至 C:\ProgramData\ASR\home\svsystems\bin。 
4. 若要從組態伺服器中移除已註冊的主機詳細資料和過時的項目資訊，請使用過時項目的來源電腦和 IP 位址，執行下列命令。 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    如果您的來源伺服器專案為 "內部部署-VM01"，ip 位址為10.0.0.4，則請改為使用下列命令。
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. 在來源電腦上重新啟動下列服務，以向組態伺服器重新註冊。 
 
    - InMage Scout 應用程式服務
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>當服務無法停止時升級失敗

當特定服務未停止時，組態伺服器升級失敗。 

若要找出問題，請瀏覽至組態伺服器上的 C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile。 如果您發現下列錯誤，請依照下列步驟來解決問題： 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

若要解決此問題：

手動停止下列服務：

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost、 
- Microsoft Azure 復原服務代理程式、 
- Microsoft Azure Site Recovery 服務、 
- tmansvc
  
若要更新組態伺服器，請再次執行[整合安裝](service-updates-how-to.md#links-to-currently-supported-update-rollups)。

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory 應用程式建立失敗

您在 Azure Active Directory (AAD) 中使用[開放虛擬化應用程式 (OVA)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) 範本建立應用程式的權限不足。

若要解決此問題，請登入 Azure 入口網站並執行下列其中一項：

- 在 AAD 中要求應用程式開發人員角色。 如需應用程式開發人員角色的詳細資訊，請參閱 [Azure Active Directory 中的系統管理員角色權限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。
- 在 AAD 中確認 [使用者可以建立應用程式]**** 旗標設為 true**。 如需詳細資訊，請參閱[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>處理序伺服器/主要目標無法與組態伺服器通訊 

處理序伺服器 (PS) 和主要目標 (MT) 模組無法與組態伺服器 (CS) 通訊，而且其狀態會顯示為未連線到 Azure 入口網站。

這通常是由於連接埠 443 發生錯誤。 使用下列步驟來解除封鎖連接埠，並重新啟用與 CS 的通訊。

**確認 MARS 代理程式正由主要目標代理程式叫用**

若要確認主要目標代理程式可以針對組態伺服器 IP 建立 TCP 工作階段，請在主要目標代理程式記錄中尋找類似以下的追蹤：

TCP \<Replace IP with CS IP here>:52739 \<Replace IP with CS IP here>:443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // 以這裡的 CS IP 取代 IP

如果您在 MT 代理程式記錄中找到類似以下的追蹤，則 MT 代理程式會在連接埠 443 上回報錯誤：

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
當其他應用程式也使用連接埠 443 時，或因為防火牆設定封鎖連接埠，有可能遇到此錯誤。

若要解決此問題：

- 確認連接埠 443 未遭到防火牆封鎖。
- 如果連接埠因為另一個使用該連接埠的應用程式而無法連線，請停止並解除安裝應用程式。
  - 如果停止應用程式並不可行，請設定全新的 CS。
- 重新啟動組態伺服器。
- 重新啟動 IIS 服務。

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>組態伺服器因為 UUID 項目不正確而未連線

當資料庫中有多個組態伺服器 (CS) 執行個體 UUID 項目時，可能會發生此錯誤。 當您複製組態伺服器 VM 時，通常會發生此問題。

若要解決此問題：

1. 從 vCenter 中移除過時/舊版 CS VM。 如需詳細資訊，請參閱[移除伺服器並停用保護](site-recovery-manage-registration-and-protection.md)。
2. 登入組態伺服器 VM 並連線到 MySQL svsdb1 資料庫。 
3. 執行以下查詢：

    > [!IMPORTANT]
    >
    > 確認您正在輸入所複製組態伺服器的 UUID 詳細資料，或不再用來保護虛擬機器的組態伺服器的過時項目。 輸入不正確的 UUID 會導致遺失所有現有受保護項目的資訊。
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. 重新整理入口網站頁面。

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>輸入認證時會發生無限登入迴圈

在組態伺服器 OVF 上輸入正確的使用者名稱和密碼之後，Azure 登入會繼續提示輸入正確的認證。

系統時間不正確時會發生此問題。

若要解決此問題：

在電腦上設定正確時間，然後重試登入。 
 