---
title: 將 Linux 電腦連接到 Azure 監視器 |Microsoft Docs
description: 本文說明如何將裝載于其他雲端或內部部署環境的 Linux 電腦，連線至使用適用于 Linux 的 Log Analytics 代理程式 Azure 監視器。
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: accd7c3ad82853c1f2af0b632326b2798f85b36b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073663"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>將 Linux 電腦連線至 Azure 監視器

若要使用 Azure 監視器來監視及管理本機資料中心或其他雲端環境中的虛擬機器或實體電腦，您需要部署 Log Analytics 代理程式，並將其設定為向 Log Analytics 工作區報告。 此代理程式也支援 Azure 自動化的混合式 Runbook 背景工作角色。

您可以使用下列其中一種方法來安裝適用于 Linux 的 Log Analytics 代理程式。 使用每種方法的詳細資料會在本文稍後提供。

* [手動下載並安裝](#install-the-agent-manually)代理程式。 當 Linux 電腦無法存取網際網路，而且將透過[Log Analytics 閘道](gateway.md)與 Azure 監視器或 Azure 自動化通訊時，這是必要的。 
* 使用裝載于 GitHub 上的[包裝函式腳本來安裝適用于 Linux 的代理程式](#install-the-agent-using-wrapper-script)。 這是在電腦直接或透過 proxy 伺服器連線時，安裝和升級代理程式的建議方法。

若要了解支援的組態，請檢閱[支援的 Linux 作業系統](log-analytics-agent.md#supported-linux-operating-systems)和[網路防火牆組態](log-analytics-agent.md#network-requirements)。

>[!NOTE]
>適用於 Linux 的 Log Analytics 代理程式無法設定為回報多個 Log Analytics 工作區。 它只能設定為同時向 System Center Operations Manager 管理群組和 Log Analytics 工作區報告，或分別回報至其中一個。

## <a name="agent-install-package"></a>代理程式安裝套件

適用于 Linux 的 Log Analytics 代理套裝程式含多個套件。 發行檔案包含下列套件，可以藉由使用參數執行 shell 組合來取得 `--extract` ：

**套件** | **版本** | **描述**
----------- | ----------- | --------------
omsagent | 1.12.15 | 適用于 Linux 的 Log Analytics 代理程式
omsconfig | 1.1.1 | Log Analytics 代理程式的設定代理程式
omi | 1.6.3 | 開放式管理基礎結構（OMI）--輕量 CIM 伺服器。 *請注意，OMI 需要根存取權，才能執行服務運作所需的 cron 作業*
scx | 1.6.3 | 作業系統效能計量的 OMI CIM 提供者
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 伺服器效能監視提供者。 僅在偵測到 Apache HTTP 伺服器時才安裝。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 伺服器效能監視提供者。 僅在偵測到 MySQL/MariaDB 伺服器時才安裝。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供者。 僅在偵測到 Docker 時才安裝。

### <a name="agent-installation-details"></a>代理程式安裝詳細資料

安裝適用于 Linux 的 Log Analytics 代理程式套件之後，會套用下列額外的全系統設定變更。 解除安裝 omsagent 封裝時，會移除這些構件。

* 會建立名為 `omsagent` 的非特殊權限使用者。 此守護程式會在此認證下執行。 
* Sudoers *include*檔案會建立在中 `/etc/sudoers.d/omsagent` 。 這會授 `omsagent` 與重新開機 syslog 和 omsagent 守護程式的授權。 如果安裝的 sudo 版本不支援 sudo *include*指示詞，這些專案將會寫入至 `/etc/sudoers` 。
* syslog 組態修改成將事件子集轉送給代理程式。 如需詳細資訊，請參閱[設定 Syslog 資料收集](data-sources-syslog.md)。

在受監視的 Linux 電腦上，代理程式會列為 `omsagent` 。 `omsconfig`是適用于 Linux 的 Log Analytics 代理程式設定代理程式，會每隔5分鐘尋找新的入口網站端設定。 新的和更新的設定會套用至位於的代理程式設定檔 `/etc/opt/microsoft/omsagent/conf/omsagent.conf` 。

## <a name="obtain-workspace-id-and-key"></a>取得工作區識別碼和金鑰

安裝適用於 Linux 的 Log Analytics 代理程式之前，您需要 Log Analytics 工作區的工作區識別碼和金鑰。 在代理程式安裝期間需要此資訊，才能正確地進行設定，並確保它能與 Azure 監視器成功通訊。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. 在 Azure 入口網站的左上角，選取 [所有服務]。 在搜尋方塊中，輸入 **Log Analytics**。 當您輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。

2. 在 Log Analytics 工作區清單中，選取您稍早建立的工作區 (您可能已經將它命名為 **DefaultLAWorkspace**)。

3. 選取 [進階設定]：

    ![Azure 入口網站中的 Log Analytics 進階設定功能表](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. 選取 [連接的來源]，然後選取 [Linux 伺服器]。

5. [工作區識別碼] 和 [主要金鑰] 右邊的值。 將兩者複製並貼到您最愛的編輯器。

## <a name="install-the-agent-manually"></a>手動安裝代理程式

適用于 Linux 的 Log Analytics 代理程式會在自動解壓縮和可安裝的 shell 腳本組合中提供。 此套件組合包含每個代理程式元件的 Debian 與 RPM 封裝，且可直接安裝或解壓縮以擷取個別的封裝。 其中一個套件組合是針對 x64 提供，一個用於 x86 架構。 

> [!NOTE]
> 針對 Azure Vm，我們建議您使用適用于 Linux 的[Azure Log ANALYTICS VM 擴充](../../virtual-machines/extensions/oms-linux.md)功能，在其上安裝代理程式。 

1. 使用 scp/sftp，將適當的套件組合（x64 或 x86）[下載](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)並傳輸至您的 Linux VM 或實體電腦。

2. 使用 `--install` 引數安裝配套。 若要在安裝期間上架至 Log Analytics 工作區，請提供稍 `-w <WorkspaceID>` `-s <workspaceKey>` 早複製的和參數。

    >[!NOTE]
    >`--upgrade`如果已安裝任何相依的封裝（例如 omi、scx、omsconfig 或其舊版），您就必須使用引數，如果已安裝適用于 Linux 的 System Center Operations Manager 代理程式，就會發生這種情況。 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. 若要將 Linux 代理程式設定為透過 Log Analytics 閘道安裝並聯機到 Log Analytics 工作區，請執行下列命令以提供 proxy、工作區識別碼和工作區金鑰參數。 您可以藉由包含，在命令列上指定此設定 `-p [protocol://][user:password@]proxyhost[:port]` 。 *Proxyhost*屬性會接受 Log Analytics 閘道伺服器的完整功能變數名稱或 IP 位址。  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    如果需要驗證，您必須指定使用者名稱和密碼。 例如: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. 若要設定 Linux 電腦以連接到 Azure Government 雲端中的 Log Analytics 工作區，請執行下列命令，提供稍早複製的工作區識別碼和主要金鑰。

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

如果您想要安裝代理程式套件，並將它設定為稍後向特定的 Log Analytics 工作區報告，請執行下列命令：

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

如果您想要從配套中解壓縮代理程式套件，而不安裝代理程式，請執行下列命令：

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>使用包裝函式腳本安裝代理程式

下列步驟會使用適用于 Linux 電腦的包裝函式腳本（可直接通訊或透過 proxy 伺服器來下載裝載于 GitHub 上的代理程式，並安裝代理程式），在 Azure 和 Azure Government 雲端中設定 Log Analytics 的代理程式。  

如果您的 Linux 電腦需要透過 proxy 伺服器與 Log Analytics 通訊，可以藉由包含在命令列上指定此設定 `-p [protocol://][user:password@]proxyhost[:port]` 。 *Protocol*屬性會接受 `http` 或 `https` ，而*proxyhost*屬性會接受 PROXY 伺服器的完整功能變數名稱或 IP 位址。 

例如：`https://proxy01.contoso.com:30443`

如果這兩種情況都需要驗證，您必須指定使用者名稱和密碼。 例如：`https://user01:password@proxy01.contoso.com:30443`

1. 若要設定 Linux 電腦連線到 Log Analytics 工作區，請執行下列命令來提供工作區識別碼和主要金鑰。 下列命令會下載代理程式、驗證其總和檢查碼，並加以安裝。
    
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

## <a name="supported-linux-hardening"></a>支援的 Linux 強化
OMS 代理程式對 Linux 具有有限的自訂支援。 

目前支援下列各項： 
- FIPs

已規劃但尚未支援下列各項：
- CIS-SELINUX

其他強化和自訂方法不受支援，也不會針對 OMS 代理程式進行規劃。  


## <a name="upgrade-from-a-previous-release"></a>從舊版升級

在每個版本中，都支援從版本 1.0.0-47 開始的舊版升級。 使用參數執行安裝 `--upgrade` ，將代理程式的所有元件升級為最新版本。

## <a name="next-steps"></a>後續步驟

- 檢閱[管理及維護適用於 Windows 和 Linux 的 Log Analytics 代理程式](agent-manage.md)，以了解如何重新設定、升級或移除虛擬機器中的代理程式。

- 如果您安裝或管理代理程式時遇到的問題，請參閱[針對 Linux 代理程式進行疑難排解](agent-linux-troubleshoot.md)。
