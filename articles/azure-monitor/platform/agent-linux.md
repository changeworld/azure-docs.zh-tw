---
title: 將 Linux 電腦連接到 Azure 監視器 |微軟文件
description: 本文介紹如何使用 Linux 的日誌分析代理將託管在其他雲端中或本地的 Linux 電腦連接到 Azure 監視器。
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: aa2356901403c7a63aa4aa96dcb38f9c0c971e58
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528355"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>將 Linux 電腦連接到 Azure 監視器

為了使用 Azure 監視器監視和管理本地資料中心或其他雲端環境中的虛擬機器或其他雲端環境中的物理電腦,您需要部署日誌分析代理並將其配置為向日誌分析工作區報告。 此代理程式也支援 Azure 自動化的混合式 Runbook 背景工作角色。

可以使用以下方法之一安裝 Linux 的日誌分析代理。 使用每種方法的詳細資料會在本文稍後提供。

* [手動下載並安裝](#install-the-agent-manually)代理。 當 Linux 計算機無法存取 Internet 並透過[日誌分析閘道](gateway.md)與 Azure 監視器或 Azure 自動化通訊時,這是必需的。 
* 使用 GitHub 上託管[的包裝文稿安裝 Linux 的代理](#install-the-agent-using-wrapper-script)程式 。 當電腦直接或透過代理伺服器與 Internet 連接時,這是安裝和升級代理的推薦方法。

若要了解支援的組態，請檢閱[支援的 Linux 作業系統](log-analytics-agent.md#supported-linux-operating-systems)和[網路防火牆組態](log-analytics-agent.md#firewall-requirements)。

>[!NOTE]
>適用於 Linux 的 Log Analytics 代理程式無法設定為回報多個 Log Analytics 工作區。 只能將其配置為同時向系統中心操作管理員管理組和日誌分析工作區報告,也可以單獨報告。

## <a name="agent-install-package"></a>代理安裝套件

Linux 的日誌分析代理由多個包組成。 發布檔包含以下套件,這些套件可透過使用`--extract`參數執行 shell 捆綁套件來取得:

**套件** | **版本** | **說明**
----------- | ----------- | --------------
omsagent | 1.12.15 | Linux 的紀錄分析代理
omsconfig | 1.1.1 | 紀錄分析代理的設定代理
omi | 1.6.3 | 開放式管理基礎架構 (OMI) -- 輕量級 CIM 伺服器。 *請注意,OMI 需要根存取才能執行服務正常執行所需的 cron 作業*
scx | 1.6.3 | 作業系統效能計量的 OMI CIM 提供者
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 伺服器效能監視提供者。 僅在偵測到 Apache HTTP 伺服器時才安裝。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 伺服器效能監視提供者。 僅在偵測到 MySQL/MariaDB 伺服器時才安裝。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供者。 僅在偵測到 Docker 時才安裝。

### <a name="agent-installation-details"></a>代理安裝詳細資訊

安裝 Linux 包的日誌分析代理後,將應用以下額外的系統範圍配置更改。 解除安裝 omsagent 封裝時，會移除這些構件。

* 會建立名為 `omsagent` 的非特殊權限使用者。 守護程序在此憑據下運行。 
* 建立檔案`/etc/sudoers.d/omsagent`*的*sudoers 這將`omsagent`授權重新啟動系統日誌和omsagent守護程式。 如果 sudo*包含*指令在 sudo 的已安裝版本中不支援`/etc/sudoers`,則這些項目寫入 。
* syslog 組態修改成將事件子集轉送給代理程式。 有關詳細資訊,請參閱設定[Syslog 資料收集](data-sources-syslog.md)。

在受監視的 Linux 電腦上,代理`omsagent`被列為 。 `omsconfig`是 Linux 設定代理的日誌分析代理,每 5 分鐘查找一次新的門戶端配置。 新的和更新的配置應用於位於的`/etc/opt/microsoft/omsagent/conf/omsagent.conf`代理配置檔。

## <a name="obtain-workspace-id-and-key"></a>取得工作區識別碼和金鑰

安裝適用於 Linux 的 Log Analytics 代理程式之前，您需要 Log Analytics 工作區的工作區識別碼和金鑰。 在代理設定期間需要此資訊來正確配置它並確保它可以與 Azure 監視器成功通訊。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. 在 Azure 入口網站的左上角，選取 [所有服務]****。 在搜尋方塊中，輸入 **Log Analytics**。 當您輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]****。

2. 在 Log Analytics 工作區清單中，選取您稍早建立的工作區  (您可能已經將它命名為 **DefaultLAWorkspace**)。

3. 選擇**進階設定**:

    ![Azure 入口網站中的 Log Analytics 進階設定功能表](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. 選取 [連接的來源]****，然後選取 [Linux 伺服器]****。

5. [工作區識別碼]**** 和 [主要金鑰]**** 右邊的值。 將兩者複製並貼到您最愛的編輯器。

## <a name="install-the-agent-manually"></a>手動安裝代理程式

Linux 的日誌分析代理在自提取和可安裝的 shell 腳本捆綁包中提供。 此套件組合包含每個代理程式元件的 Debian 與 RPM 封裝，且可直接安裝或解壓縮以擷取個別的封裝。 為 x64 提供了一個捆綁包,為 x86 體系結構提供了一個捆綁包。 

> [!NOTE]
> 對於 Azure VM,我們建議您使用 Linux 的 Azure[日誌分析 VM 擴展](../../virtual-machines/extensions/oms-linux.md)在它們上安裝代理。 

1. 使用 scp/sftp 將適當的捆綁套件 (x64 或 x86)[下載](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)並傳輸到 Linux VM 或物理電腦。

2. 使用`--install`參數安裝捆綁包。 要在安裝期間將日誌分析工作區裝上板,請`-w <WorkspaceID>``-s <workspaceKey>`提供 前面複製的 和 參數。

    >[!NOTE]
    >如果安裝了 omi、scx、omsconfig 或其舊版本等從屬包,則`--upgrade`需要使用 參數,如果已安裝 Linux 的系統中心操作管理器代理,則無需使用參數。 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. 要將 Linux 代理設定為透過日誌分析閘道安裝和連接到日誌分析工作區,請運行以下命令,提供代理、工作區 ID 和工作區金鑰參數。 可以通過`-p [protocol://][user:password@]proxyhost[:port]`包括在命令行上指定此配置。 *代理主機*屬性接受日誌分析閘道伺服器的完全限定的網域名稱或 IP 位址。  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    如果需要身份驗證,則需要指定使用者名和密碼。 例如： 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. 要將 Linux 計算機設定為連接到 Azure 政府雲中的日誌分析工作區,請運行以下命令,提供前面複製的工作區 ID 和主金鑰。

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

如果要安裝代理套件並將其設定為稍後向特定日誌分析工作區報告,請執行以下命令:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

如果要在不安裝代理的情況下從捆綁包中提取代理包,則運行以下命令:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>使用包裝文稿安裝代理

以下步驟使用 Linux 電腦的包裝腳本配置 Azure 和 Azure 政府雲中日誌分析代理的設置,這些腳本可以直接或透過代理伺服器進行通訊,以下載在 GitHub 上託管的代理並安裝代理。  

如果 Linux 計算機需要透過代理伺服器與日誌分析進行通訊,則`-p [protocol://][user:password@]proxyhost[:port]`可以透過包括在命令列上指定此配置。 *協定*屬性`http`接受`https`或,*代理主機*屬性接受代理伺服器完全限定的網域名稱或 IP 位址。 

例如： `https://proxy01.contoso.com:30443`

如果在這兩種情況下都需要身份驗證,則需要指定使用者名和密碼。 例如： `https://user01:password@proxy01.contoso.com:30443`

1. 要將 Linux 計算機設定為連接到日誌分析工作區,請運行以下命令,提供工作區 ID 和主鍵。 下列命令會下載代理程式、驗證其總和檢查碼，並加以安裝。
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    當您的 Proxy 伺服器需要驗證時，下列命令會包含 `-p` Proxy 參數和範例語法：

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. 若要設定 Linux 電腦以連線到 Azure Government 雲端中的 Log Analytics 工作區，請執行下列命令，提供稍早複製的工作區識別碼和主索引鍵。 下列命令會下載代理程式、驗證其總和檢查碼，並加以安裝。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    當您的 Proxy 伺服器需要驗證時，下列命令會包含 `-p` Proxy 參數和範例語法：

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 執行下列命令來重新啟動代理程式： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>從舊版升級

每個版本都支援從以前的版本(從版本 1.0.0-47 開始)進行升級。 使用`--upgrade`參數執行安裝,將代理的所有元件升級到最新版本。

## <a name="next-steps"></a>後續步驟

- 檢視[為 Windows 和 Linux 管理和維護日誌分析代理](agent-manage.md),瞭解如何從虛擬機器重新配置、升級或刪除代理。

- 如果您安裝或管理代理程式時遇到的問題，請參閱[針對 Linux 代理程式進行疑難排解](agent-linux-troubleshoot.md)。
