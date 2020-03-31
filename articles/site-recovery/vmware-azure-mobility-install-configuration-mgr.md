---
title: 自動化移動服務，用於在 Azure 網站恢復中恢復安裝
description: 如何通過 Azure 網站恢復自動安裝用於 VMware/物理伺服器災害復原的移動服務。
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252222"
---
# <a name="automate-mobility-service-installation"></a>自動移動服務安裝

本文介紹如何在[Azure 網站恢復](site-recovery-overview.md)中自動安裝和更新移動服務代理。

將網站恢復部署到 Azure 時，將網站恢復用於本地 VMware VM 和物理伺服器的災害復原，在要複製的每台電腦上安裝移動服務代理。 移動服務捕獲電腦上寫入的資料，並將它們轉發到網站恢復進程伺服器進行複製。 您可以通過以下幾種方式部署移動服務：

- **推送安裝**：在 Azure 門戶中為電腦啟用複製時，讓網站恢復安裝移動服務代理。
- **手動安裝**：在每台電腦上手動安裝移動服務。 [瞭解有關](vmware-physical-mobility-service-overview.md)推送和手動安裝的更多詳細資訊。
- **自動部署**：使用軟體部署工具（如 Microsoft 端點組態管理員）或協力廠商工具（如 JetPatch）自動安裝。

自動安裝和更新可在：

- 您的組織不允許在受保護的伺服器上推送安裝。
- 公司政策要求定期更改密碼。 您必須為推送安裝指定密碼。
- 您的安全性原則不允許為特定電腦添加防火牆異常。
- 您充當託管服務提供者，並且不希望提供使用網站恢復推送安裝所需的客戶電腦憑據。
- 您需要同時將代理安裝擴展到大量伺服器。
- 您希望在計畫維護時段內安排安裝和升級。

## <a name="prerequisites"></a>Prerequisites

要自動安裝，您需要以下專案：

- 已部署的軟體安裝解決方案，如[組態管理員](/configmgr/)或[JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/)。
- [在 Azure](tutorial-prepare-azure.md)和[本地](vmware-azure-tutorial-prepare-on-premises.md)部署先決條件，用於 VMware 災害復原或[物理伺服器](physical-azure-disaster-recovery.md)災害復原。 查看災害復原[的支援要求](vmware-physical-azure-support-matrix.md)。

## <a name="prepare-for-automated-deployment"></a>為自動部署做好準備

下表總結了自動化移動服務部署的工具和流程。

**工具** | **詳細資料** | **指示**
--- | --- | ---
**組態管理員** | 1. 驗證您是否具備上面列出的[先決條件](#prerequisites)。 <br/><br/> 2. 通過設置源環境（包括下載 OVA 檔）使用 OVF 範本將網站恢復佈建服務器部署為 VMware VM 來部署災害復原。<br/><br/> 3. 將佈建服務器註冊到網站恢復服務，設置目標 Azure 環境，並配置複寫原則。<br/><br/> 4. 對於自動移動服務部署，您可以創建包含佈建服務器密碼和移動服務安裝檔的網路共用。<br/><br/> 5. 創建包含安裝或更新的組態管理員包，並為移動服務部署做好準備。<br/><br/> 6. 然後，您可以為安裝了移動服務的電腦啟用複製到 Azure。 | [使用組態管理員自動執行](#automate-with-configuration-manager)
**JetPatch** | 1. 驗證您是否具備上面列出的[先決條件](#prerequisites)。 <br/><br/> 2. 通過設置源環境（包括下載和部署 JetPatch 代理管理器以在網站恢復環境中使用 OVF 範本）來部署災害復原。<br/><br/> 3. 將佈建服務器註冊為網站恢復，設置目標 Azure 環境，並配置複寫原則。<br/><br/> 4. 對於自動部署，初始化和完成 JetPatch 代理管理器配置。<br/><br/> 5. 在 JetPatch 中，您可以創建網站修復原則，以自動部署和升級移動服務代理。 <br/><br/> 6. 然後，您可以為安裝了移動服務的電腦啟用複製到 Azure。 | [使用 JetPatch 代理管理器實現自動化](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [在 JetPatch 中排除代理安裝故障](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>使用組態管理員自動執行

### <a name="prepare-the-installation-files"></a>準備安裝檔

1. 請確保您已具備先決條件。
1. 創建回合組態伺服器的電腦可以訪問的安全網路檔共用 （SMB 共用）。
1. 在組態管理員中，對要安裝或更新移動服務的[伺服器進行分類](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections)。 一個集合應包含所有 Windows 伺服器，另一個包含所有 Linux 伺服器。
1. 在網路共用上，創建一個資料夾：

   - 要在 Windows 電腦上安裝，請創建一個名為_MobSvcWindows_的資料夾。
   - 要在 Linux 電腦上安裝，請創建一個名為_MobSvcLinux_的資料夾。

1. 登入設定伺服器電腦。
1. 在佈建服務器電腦上，打開管理命令提示符。
1. 要生成密碼檔，運行以下命令：

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. 將_MobSvc.passphrase 檔_複製到 Windows 資料夾和 Linux 資料夾。
1. 要流覽到包含安裝檔的資料夾，請運行以下命令：

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. 將這些安裝檔案複製到網路共用：

   - 對於 Windows，請將_Microsoft ASR_UA_version_Windows_GA_date_Release.exe_複製到_MobSvcWindows_。
   - 對於 Linux，請將以下檔案複製到_MobSvcLinux_：
     - _微軟-ASR_UARHEL6-64發佈.tar.gz_
     - _微軟-ASR_UARHEL7-64發佈.tar.gz_
     - _微軟-ASR_UASLES11-SP3-64發行.tar.gz_
     - _微軟-ASR_UASLES11-SP4-64發行.tar.gz_
     - _微軟-ASR_UAOL6-64發佈.tar.gz_
     - _微軟-ASR_UAUBUNTU-14.04-64發佈.tar.gz_

1. 如以下過程所述，請將代碼複製到 Windows 或 Linux 資料夾。 我們假設：

   - 佈建服務器的 IP 位址為`192.168.3.121`。
   - 安全網路檔共用為`\\ContosoSecureFS\MobilityServiceInstallers`。

### <a name="copy-code-to-the-windows-folder"></a>將代碼複製到 Windows 資料夾

複製以下代碼：

- 將_MobSvcWindows_資料夾中的代碼保存為_安裝.bat_。
- 將此`[CSIP]`腳本中的預留位置替換為佈建服務器的 IP 位址的實際值。
- 該腳本支援移動服務代理的新安裝，以及對已安裝的代理的更新。

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>將代碼複製到 Linux 資料夾

複製以下代碼：

- 將_MobSvcLinux_資料夾中的代碼另存為_install_linux._
- 將此`[CSIP]`腳本中的預留位置替換為佈建服務器的 IP 位址的實際值。
- 該腳本支援移動服務代理的新安裝，以及對已安裝的代理的更新。

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>建立套件

1. 登錄到組態管理員主控台並轉到**軟體庫** > **應用程式管理** > **包**。
1. 按右鍵 **"包** > **創建包**"。
1. 提供包詳細資訊，包括名稱、說明、製造商、語言和版本。
1. 選擇**此包包含原始檔案**。
1. 按一下 **"流覽**"，然後選擇包含相關安裝程式 _（MobSvcWindows_或_MobSvcLinux）_ 的網路共用和資料夾。 然後，選擇 **"下一步**"。

   ![建立套件和程式精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. 在**選擇要創建頁面的程式類型**中，選擇"**下一步****標準程式** > "。

   ![建立套件和程式精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. 在 **"指定有關此標準程式頁的資訊**"中，指定以下值：

    **參數** | **視窗值** | **Linux 值**
    --- | --- | ---
    **名稱** | 安裝 Microsoft Azure 行動服務 (Windows) | 安裝 Microsoft Azure 移動服務 （Linux）。
    **命令列** | install.bat | ./install_linux.sh
    **程式可以執行** | 使用者是否登入 | 使用者是否登入
    **其他參數** | 使用預設設置 | 使用預設設置

   ![建立套件和程式精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. 在**指定此標準程式的要求中**，執行以下任務：

   - 對於 Windows 電腦，選擇**此程式只能在指定的平臺上運行**。 然後，選擇[受支援的 Windows 作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)，然後選擇 **"下一步**"。
   - 對於 Linux 電腦，選擇**此程式可以在任何平臺上運行**。 然後選擇 **"下一步**"。

1. 完成精靈。

### <a name="deploy-the-package"></a>部署套件

1. 在組態管理員主控台中，按右鍵包並選擇 **"分發內容**"。

   ![Configuration Manager 主控台的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. 選取應該將套件複製過去的發佈點。 [深入了解](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points)。
1. 完成精靈。 套件便會開始複寫至指定的發佈點。
1. 包分發完成後，按右鍵包>**部署**。

   ![Configuration Manager 主控台的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. 選擇您以前創建的 Windows 或 Linux 設備集合。
1. 在 **"指定內容目標**"頁上，選擇**發佈點**。
1. 在 **"指定設置以控制此軟體的部署方式"** 頁中，將 **"使用目的"** 設置為 **"必需**"。

   ![部署軟體精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. 在 **"指定此部署的計畫**"中，設置計畫。 [深入了解](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched)。

   - 移動服務按照您指定的計畫進行安裝。
   - 為了避免不必要的重新開機，請排定在每月維護期間或軟體更新期間安裝套件。

1. 在 **"發佈點"** 頁上，配置設置並完成嚮導。
1. 在組態管理員 主控台中監視部署進度。 轉到**監視** > **Deployments** > _部署\<包名稱\>_。

### <a name="uninstall-the-mobility-service"></a>卸載移動服務

您可以創建組態管理員包以卸載移動服務。 例如，以下腳本卸載移動服務：

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>後續步驟

為 VM[啟用複製](vmware-azure-enable-replication.md)。
