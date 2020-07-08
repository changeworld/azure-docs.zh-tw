---
title: 將行動服務自動化，以在 Azure Site Recovery 中進行安裝的嚴重損壞修復
description: 如何使用 Azure Site Recovery 自動安裝適用于 VMware/physical server 嚴重損壞修復的行動服務。
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77252222"
---
# <a name="automate-mobility-service-installation"></a>自動安裝行動服務

本文說明如何在[Azure Site Recovery](site-recovery-overview.md)中，自動執行行動服務代理程式的安裝和更新。

當您將內部部署 VMware Vm 和實體伺服器的嚴重損壞修復 Site Recovery 部署到 Azure 時，您會在您要複寫的每部機器上安裝行動服務代理程式。 行動服務會在機器上捕獲資料寫入，然後將它們轉送到 Site Recovery 的進程伺服器進行複寫。 您可以透過幾種方式來部署行動服務：

- **推入安裝**：當您為 Azure 入口網站中的機器啟用複寫時，讓 Site Recovery 安裝行動服務代理程式。
- **手動安裝**：在每部電腦上手動安裝行動服務。 [深入瞭解](vmware-physical-mobility-service-overview.md)推送和手動安裝。
- **自動化部署**：使用軟體部署工具（例如 Microsoft Endpoint Configuration Manager）或協力廠商工具（例如 JetPatch）來自動化安裝。

自動安裝和更新提供下列解決方案：

- 您的組織不允許在受保護的伺服器上進行推送安裝。
- 您的公司原則需要定期變更密碼。 您必須指定推入安裝的密碼。
- 您的安全性原則不允許為特定機器新增防火牆例外。
- 您擔任的是主機服務提供者，而不想提供使用 Site Recovery 進行推送安裝所需的客戶電腦認證。
- 您需要同時將代理程式安裝調整到許多伺服器。
- 您想要在規劃的維護時段排程安裝和升級。

## <a name="prerequisites"></a>必要條件

若要自動安裝，您需要下列專案：

- 已部署的軟體安裝解決方案，例如[Configuration Manager](/configmgr/)或[JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/)。
- 在[Azure](tutorial-prepare-azure.md)和[內部](vmware-azure-tutorial-prepare-on-premises.md)部署環境中，適用于 VMware 嚴重損壞修復或[實體伺服器](physical-azure-disaster-recovery.md)嚴重損壞修復的部署必要條件。 請參閱嚴重損壞修復的[支援需求](vmware-physical-azure-support-matrix.md)。

## <a name="prepare-for-automated-deployment"></a>準備自動部署

下表摘要說明自動化行動服務部署的工具和程式。

**工具** | **詳細資料** | **指示**
--- | --- | ---
**Configuration Manager** | 1. 確認您有上述[必要條件](#prerequisites)。 <br/><br/> 2. 藉由設定來源環境來部署嚴重損壞修復，包括下載 OVA 檔案，以使用 OVF 範本將 Site Recovery 設定伺服器部署為 VMware VM。<br/><br/> 3. 向 Site Recovery 服務註冊設定伺服器、設定目標 Azure 環境，以及設定複寫原則。<br/><br/> 4. 若要進行自動化行動服務部署，您可以建立包含設定伺服器複雜密碼和行動服務安裝檔案的網路共用。<br/><br/> 5. 建立包含安裝或更新的 Configuration Manager 套件，並準備行動服務部署。<br/><br/> 6. 接著，您可以針對已安裝行動服務的機器，啟用 Azure 的複寫。 | [使用 Configuration Manager 自動化](#automate-with-configuration-manager)
**JetPatch** | 1. 確認您有上述[必要條件](#prerequisites)。 <br/><br/> 2. 藉由設定來源環境來部署嚴重損壞修復，包括使用 OVF 範本下載和部署 Site Recovery 環境中 Azure Site Recovery 的 JetPatch 代理程式管理員。<br/><br/> 3. 向 Site Recovery 註冊設定伺服器、設定目標 Azure 環境，以及設定複寫原則。<br/><br/> 4. 若要進行自動化部署，請初始化並完成 JetPatch 代理程式管理員設定。<br/><br/> 5. 在 JetPatch 中，您可以建立 Site Recovery 原則，以自動化行動服務代理程式的部署和升級。 <br/><br/> 6. 接著，您可以針對已安裝行動服務的機器，啟用 Azure 的複寫。 | [使用 JetPatch 代理程式管理員進行自動化](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [針對 JetPatch 中的代理程式安裝進行疑難排解](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>使用 Configuration Manager 自動化

### <a name="prepare-the-installation-files"></a>準備安裝檔案

1. 請確定您已準備好必要條件。
1. 建立可由執行設定伺服器的電腦存取的安全網路檔案共用（SMB 共用）。
1. 在 Configuration Manager 中，將您想要安裝或更新行動服務[的伺服器分類](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections)。 其中一個集合應該包含所有的 Windows 伺服器，也就是其他所有的 Linux 伺服器。
1. 在網路共用上，建立資料夾：

   - 若要在 Windows 電腦上安裝，請建立名為_MobSvcWindows_的資料夾。
   - 若要在 Linux 機器上安裝，請建立名為_MobSvcLinux_的資料夾。

1. 登入設定伺服器電腦。
1. 在設定伺服器電腦上，開啟系統管理命令提示字元。
1. 若要產生複雜密碼檔案，請執行此命令：

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. 將 [ _mobsvc.passphrase_ ] 檔案複製到 [Windows] 資料夾和 [Linux] 資料夾。
1. 若要流覽至包含安裝檔案的資料夾，請執行下列命令：

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. 將這些安裝檔案複製到網路共用：

   - 若是 Windows，請將_Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_複製到_MobSvcWindows_。
   - 若為 Linux，請將下列檔案複製到_MobSvcLinux_：
     - _Microsoft ASR_UARHEL6-64release. tar. gz_
     - _Microsoft ASR_UARHEL7-64release. tar. gz_
     - _Microsoft ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft ASR_UAOL6-64release. tar. gz_
     - _ASR_UAUBUNTU 14.04-64release. gz_

1. 如下列程式所述，將程式碼複製到 Windows 或 Linux 資料夾。 我們假設：

   - 設定伺服器的 IP 位址是 `192.168.3.121` 。
   - 安全網路檔案共用是 `\\ContosoSecureFS\MobilityServiceInstallers` 。

### <a name="copy-code-to-the-windows-folder"></a>將程式碼複製到 Windows 資料夾

複製下列程式碼：

- 將_MobSvcWindows_資料夾中的程式碼儲存為_install.bat_。
- 將 `[CSIP]` 此腳本中的預留位置取代為設定伺服器 IP 位址的實際值。
- 此腳本支援行動服務代理程式的新安裝，以及已安裝之代理程式的更新。

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

### <a name="copy-code-to-the-linux-folder"></a>將程式碼複製到 Linux 資料夾

複製下列程式碼：

- 將_MobSvcLinux_資料夾中的程式碼儲存為_install_linux sh_。
- 將 `[CSIP]` 此腳本中的預留位置取代為設定伺服器 IP 位址的實際值。
- 此腳本支援行動服務代理程式的新安裝，以及已安裝之代理程式的更新。

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

1. 登入 Configuration Manager 主控台，並移至 [**軟體程式庫**] [  >  **應用程式管理**] [  >  **套件**]。
1. 以滑鼠右鍵按一下 [**封裝**] [  >  **建立套件**]。
1. 提供套件詳細資料，包括名稱、描述、製造商、語言和版本。
1. 選取 [**此套件包含來源**檔案]。
1. 按一下 **[流覽]**，然後選取包含相關安裝程式的網路共用和資料夾（_MobSvcWindows_或_MobSvcLinux_）。 然後，選取 [下一步]。

   ![建立套件和程式精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. 在 **[選擇您要建立的程式類型**] 頁面上，選取 [**標準程式**  >  **] [下一步]**。

   ![建立套件和程式精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. 在 [**指定此標準程式的相關資訊**] 頁面中，指定下列值：

    **參數** | **Windows 值** | **Linux 值**
    --- | --- | ---
    **名稱** | 安裝 Microsoft Azure 行動服務 (Windows) | 安裝 Microsoft Azure 行動服務（Linux）。
    **命令列** | install.bat | ./install_linux.sh
    **程式可以執行** | 使用者是否登入 | 使用者是否登入
    **其他參數** | 使用預設設定 | 使用預設設定

   ![建立套件和程式精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. 在 **[指定此標準程式的需求**] 中，執行下列工作：

   - 若為 Windows 機器，請選取 [**此程式只能在指定的平臺上執行**]。 然後選取支援的[Windows 作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)，然後選取 **[下一步]**。
   - 針對 Linux 電腦，請選取 [**此程式可以在任何平臺上執行**]。 然後選取 [下一步]。

1. 完成精靈。

### <a name="deploy-the-package"></a>部署套件

1. 在 Configuration Manager 主控台中，以滑鼠右鍵按一下套件，然後選取 [**發佈內容**]。

   ![Configuration Manager 主控台的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. 選取應該將套件複製過去的發佈點。 [深入了解](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points)。
1. 完成精靈。 套件便會開始複寫至指定的發佈點。
1. 封裝散發完成後，以滑鼠右鍵按一下封裝 >**部署**]。

   ![Configuration Manager 主控台的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. 選取您先前建立的 Windows 或 Linux 裝置集合。
1. 在 [**指定內容目的地**] 頁面上，選取 [**發佈點**]。
1. 在 [**指定控制此軟體部署方式的設定**] 頁面中，將 [**目的**] 設定為 [**必要**]。

   ![部署軟體精靈的螢幕擷取畫面](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. 在 [**指定此部署的排程**] 中，設定排程。 [深入了解](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched)。

   - 行動服務會根據您指定的排程安裝。
   - 為了避免不必要的重新開機，請排定在每月維護期間或軟體更新期間安裝套件。

1. 在 [**發佈點**] 頁面上，設定設定並完成嚮導。
1. 在 Configuration Manager 主控台中監視部署進度。 移至 [**監視**] [  >  **部署**]  >  _\<your package name\>_ 。

### <a name="uninstall-the-mobility-service"></a>卸載行動服務

您可以建立 Configuration Manager 封裝來卸載行動服務。 例如，下列腳本會卸載行動服務：

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

[啟用](vmware-azure-enable-replication.md)vm 的複寫。
