---
title: 在 Linux 電腦上安裝 Log Analytics 代理程式
description: 本文說明如何將在其他雲端或內部部署環境中裝載的 Linux 電腦連線至適用于 Linux 的 Log Analytics 代理程式 Azure 監視器。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: e1dbf5e20aa206189397cab26e9b867f4942e1d5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886833"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>在 Linux 電腦上安裝 Log Analytics 代理程式
本文將詳細說明如何使用下列方法，在 Linux 電腦上安裝 Log Analytics 代理程式：

* 使用裝載于 GitHub 上的[包裝函式腳本，安裝適用于 Linux 的代理程式](#install-the-agent-using-wrapper-script)。 當電腦直接或透過 proxy 伺服器連線時，建議使用這種方法來安裝和升級代理程式。
* [手動下載並安裝](#install-the-agent-manually) 代理程式。 當 Linux 電腦無法存取網際網路，而且將透過 [Log Analytics 閘道](gateway.md)與 Azure 監視器或 Azure 自動化通訊時，這是必要的。 

>[!IMPORTANT]
> 本文所述的安裝方法通常用於內部部署或其他雲端中的虛擬機器。 請參閱 [安裝選項](log-analytics-agent.md#installation-options) ，以取得可用於 Azure 虛擬機器的更有效率選項。



## <a name="supported-operating-systems"></a>支援的作業系統

如需 Log Analytics 代理程式所支援的 Linux 發行版本清單，請參閱 [Azure 監視器代理](agents-overview.md#supported-operating-systems) 程式的總覽。

>[!NOTE]
>OpenSSL 1.1.0 只支援用於 x86_x64 平台 (64 位元)，1.x 之前的 OpenSSL 則不支援用於任何平台。

>[!NOTE]
>不支援在容器中執行 Log Analytics Linux 代理程式。 如果您需要監視容器，請利用適用于 Docker 主機的 [容器監視解決方案](../insights/containers.md) 或適用于 Kubernetes [的容器的 Azure 監視器](../insights/container-insights-overview.md) 。

從 2018 年 8 月之後所發行的版本開始，我們會對支援模型進行下列變更：  

* 只支援伺服器版本，不支援用戶端版本。  
* 請將焦點放在任何 [Azure Linux 背書散發版本](../../virtual-machines/linux/endorsed-distros.md)的支援上。 請注意，從新的散發版本/版本受到 Azure Linux 背書到開始支援 Log Analytics Linux 代理程式，時間上可能會有一些延遲。
* 列出的每個主要版本都支援所有次要版本。
* 不支援製造商結束支援日期已過的版本。
* 僅支援 VM 映射;不支援容器（甚至是衍生自官方發行版本發行者映射的容器）。
* 不支援新版 AMI。  
* 只支援預設執行 SSL 1.x 的版本。

>[!NOTE]
>如果您使用的散發版本或版本目前不受支援，且不符合支援模型，建議您為此存放庫建立分支，以認可 Microsoft 支援服務將不會對分支代理程式版本提供協助。

### <a name="python-requirement"></a>Python 需求

從代理程式版本1.13.27 開始，Linux 代理程式將同時支援 Python 2 和3。 我們一律建議使用最新的代理程式。 

如果您使用的是舊版的代理程式，則必須讓虛擬機器預設使用 python 2。 如果您的虛擬機器預設使用未包含 Python 2 的發行版本，則您必須安裝它。 下列範例命令會在不同的散發版本上安裝 Python 2。

 - Red Hat、CentOS、Oracle： `yum install -y python2`
 - Ubuntu、Debian： `apt-get install -y python2`
 - SUSE：`zypper install -y python2`

Python2 可執行檔必須以 *python* 為別名。 以下是您可以用來設定此別名的其中一種方法：

1. 執行下列命令以移除任何現有的別名。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 執行下列命令以建立別名。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>支援的 Linux 強化
OMS 代理程式對 Linux 有有限的自訂支援。 

目前支援下列各項： 
- Fips

下列為考慮但尚不支援的事項：
- Cis
- SELINUX

不支援其他強化和自訂方法，也不會規劃 OMS 代理程式。  

## <a name="agent-prerequisites"></a>代理程式必要條件

下表強調代理程式將安裝的 [支援 Linux 散發版本](#supported-operating-systems) 所需的套件。

|必要的套件 |描述 |最小版本 |
|-----------------|------------|----------------|
|Glibc |    GNU C 程式庫 | 2.5-12 
|Openssl    | OpenSSL 程式庫 | 1.0.x 或 1.1.x |
|Curl | cURL Web 用戶端 | 7.15.5 |
|Python | | 2.6 + 或 3.3 +
|Python-ctypes | | 
|PAM | 插入式驗證模組 | | 

>[!NOTE]
>需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。 Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。

## <a name="network-requirements"></a>網路需求
請參閱 [Log Analytics 代理程式總覽](log-analytics-agent.md#network-requirements) ，以瞭解 Linux 代理程式的網路需求。

## <a name="agent-install-package"></a>代理程式安裝套件

適用于 Linux 的 Log Analytics 代理程式是由多個套件所組成。 發行檔案包含下列套件，可透過使用參數執行 shell 組合來取得 `--extract` ：

**套件** | **版本** | **描述**
----------- | ----------- | --------------
omsagent | 1.13.9 | 適用于 Linux 的 Log Analytics 代理程式
omsconfig | 1.1.1 | Log Analytics 代理程式的設定代理程式
omi | 1.6.4 | 開放式管理基礎結構 (OMI) --輕量 CIM 伺服器。 *請注意，OMI 需要 root 存取權，才能執行服務運作所需的 cron 作業。*
scx | 1.6.4 | 作業系統效能計量的 OMI CIM 提供者
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 伺服器效能監視提供者。 僅在偵測到 Apache HTTP 伺服器時才安裝。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 伺服器效能監視提供者。 僅在偵測到 MySQL/MariaDB 伺服器時才安裝。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供者。 僅在偵測到 Docker 時才安裝。

### <a name="agent-installation-details"></a>代理程式安裝詳細資料

安裝適用于 Linux 的 Log Analytics 代理程式套件之後，會套用下列額外的全系統設定變更。 解除安裝 omsagent 封裝時，會移除這些構件。

* 會建立名為 `omsagent` 的非特殊權限使用者。 此守護程式會在此認證下執行。 
* Sudoers *include* 檔案會建立在中 `/etc/sudoers.d/omsagent` 。 這會授 `omsagent` 與重新開機 syslog 和 omsagent 守護程式的授權。 如果已安裝的 sudo 版本不支援 sudo *include* 指示詞，這些專案將會寫入至 `/etc/sudoers` 。
* syslog 組態修改成將事件子集轉送給代理程式。 如需詳細資訊，請參閱 [設定 Syslog 資料收集](data-sources-syslog.md)。

在受監視的 Linux 電腦上，代理程式會列為 `omsagent` 。 `omsconfig` 是適用于 Linux 的 Log Analytics 代理程式設定代理程式，每隔5分鐘會尋找新的入口網站端設定。 新的和更新的設定會套用到位於的代理程式設定檔 `/etc/opt/microsoft/omsagent/conf/omsagent.conf` 。

## <a name="install-the-agent-using-wrapper-script"></a>使用包裝函式腳本來安裝代理程式

下列步驟會在 Azure 中設定 Log Analytics 的代理程式設定，並使用適用于 Linux 電腦的包裝函式腳本（可直接或透過 proxy 伺服器進行通訊，以下載裝載在 GitHub 上的代理程式並安裝代理程式）來 Azure Government 雲端。  

如果您的 Linux 電腦需要透過 proxy 伺服器與 Log Analytics 通訊，您可以在命令列上指定此設定，包括 `-p [protocol://][user:password@]proxyhost[:port]` 。 *Protocol* 屬性會接受 `http` 或 `https` ，而 *proxyhost* 屬性會接受 PROXY 伺服器的完整功能變數名稱或 IP 位址。 

例如：`https://proxy01.contoso.com:30443`

如果任何一種情況都需要驗證，您必須指定使用者名稱和密碼。 例如：`https://user01:password@proxy01.contoso.com:30443`

1. 若要設定 Linux 電腦以連線到 Log Analytics 工作區，請執行下列命令來提供工作區識別碼和主要金鑰。 下列命令會下載代理程式、驗證其總和檢查碼，並加以安裝。
    
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


## <a name="install-the-agent-manually"></a>手動安裝代理程式

適用于 Linux 的 Log Analytics 代理程式會在自動解壓縮和可安裝的 shell 腳本套件組合中提供。 此套件組合包含每個代理程式元件的 Debian 與 RPM 封裝，且可直接安裝或解壓縮以擷取個別的封裝。 其中一個套件組合是針對 x64 提供，另一個適用于 x86 架構。 

> [!NOTE]
> 針對 Azure Vm，建議您使用適用于 Linux 的 [Azure Log ANALYTICS VM 擴充](../../virtual-machines/extensions/oms-linux.md) 功能，在這些 vm 上安裝代理程式。 

1. 使用 scp/sftp[下載](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)適當的配套 (x64 或 x86) 至 Linux VM 或實體電腦。

2. 使用引數安裝套件組合 `--install` 。 若要在安裝期間上架到 Log Analytics 工作區，請提供稍 `-w <WorkspaceID>` `-s <workspaceKey>` 早複製的和參數。

    >[!NOTE]
    >如果已安裝 `--upgrade` 適用于 Linux 的 System Center Operations Manager 代理程式，則需要使用引數（如 omi、scx、omsconfig 或其較舊版本）。 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. 若要設定 Linux 代理程式以透過 Log Analytics 閘道安裝和連線到 Log Analytics 工作區，請執行下列命令以提供 proxy、工作區識別碼和工作區金鑰參數。 您可以在命令列上指定此設定，方法是包含 `-p [protocol://][user:password@]proxyhost[:port]` 。 *Proxyhost* 屬性會接受 Log Analytics 閘道伺服器的完整功能變數名稱或 IP 位址。  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    如果需要驗證，您必須指定使用者名稱和密碼。 例如： 
    
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

如果您想要從套件組合解壓縮代理程式封裝，而不安裝代理程式，請執行下列命令：

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>從舊版升級

每個版本都支援從 1.0.0-47 版開始的舊版升級。 使用參數執行安裝， `--upgrade` 將代理程式的所有元件升級為最新版本。

## <a name="cache-information"></a>快取資訊
適用于 Linux 的 Log Analytics 代理程式的資料會在本機電腦上快取（ *% STATE_DIR_WS/out_oms_common*. 緩衝區 *），然後再傳送至 Azure 監視器。 自訂記錄檔資料會以 *% STATE_DIR_WS/out_oms_blob* 緩衝 * 緩衝。 某些 [方案和資料類型](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)的路徑可能不同。

代理程式每隔20秒會嘗試上傳一次。 如果失敗，則會以指數方式增加時間長度，直到第二次嘗試前30秒，第三個120秒之前的60秒 .。。在重試之間，最多可達16分鐘，直到再次成功連接為止。 代理程式會在捨棄並移至下一個資料區塊之前，重試最多6次的資料。 這會繼續進行，直到代理程式可以成功上傳為止。 這表示，資料可能會在捨棄前大約30分鐘內經過緩衝處理。

預設的快取大小為 10 MB，但可以在 [omsagent](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)檔案中修改。


## <a name="next-steps"></a>後續步驟

- 檢閱[管理及維護適用於 Windows 和 Linux 的 Log Analytics 代理程式](agent-manage.md)，以了解如何重新設定、升級或移除虛擬機器中的代理程式。

- 如果您安裝或管理代理程式時遇到的問題，請參閱[針對 Linux 代理程式進行疑難排解](agent-linux-troubleshoot.md)。
