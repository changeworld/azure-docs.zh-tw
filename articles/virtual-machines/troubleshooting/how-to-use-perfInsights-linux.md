---
title: 如何在 Microsoft Azure 中使用 PerfInsights Linux |Microsoft Docs
description: 學習如何使用 PerfInsights 針對 Linux VM 效能問題進行疑難排解。
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: d4d25d8e79c30933546af2b823e328a652f482eb
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360526"
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) 是一種自助診斷工具，可收集和分析診斷資料，並提供報告來協助針對 Azure 中的 Linux 虛擬機器效能問題進行疑難排解。 PerfInsights 可在支援的虛擬機器上以獨立工具的形式執行，或直接從入口網站使用 [Azure 虛擬機器的 Performance Diagnostics](performance-diagnostics.md)來執行。

如果您遇到虛擬機器的效能問題，在連絡支援人員之前，請先執行這個工具。

## <a name="supported-troubleshooting-scenarios"></a>支援的疑難排解案例

PerfInsights 可以收集並分析多種資訊。 下列幾節會說明常見案例。

### <a name="quick-performance-analysis"></a>快速效能分析

此案例會收集基本資訊，例如虛擬機器的儲存和硬體設定、各種記錄，包括：

- 作業系統資訊

- PCI 裝置資訊

- 一般的來賓 OS 記錄

- 組態檔

- 儲存體資訊

- Azure 虛擬機器設定 (使用 [azure Instance Metadata Service](../windows/instance-metadata-service.md)) 收集

- 執行中進程、磁片、記憶體和 CPU 使用量的清單

- 網路資訊

這是被動的資訊集合，應該不會影響系統。

>[!Note]
>下列每個案例都會自動包含快速效能分析案例：

### <a name="performance-analysis"></a>效能分析

此案例類似于快速效能分析，但可讓您捕獲更長期間的診斷資訊。

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights 收集何種資訊？

系統會收集 Linux 虛擬機器、作業系統、封鎖裝置、高資源取用者、設定和各種記錄的相關資訊。 以下是更多詳細資料：

- 作業系統
  - Linux 散發套件和版本
  - 核心資訊
  - 驅動程式資訊

- 硬體
  - PCI 裝置 [ `*` ]

- 進程和記憶體
  -  (工作名稱、使用的記憶體、開啟的檔案) 的進程清單
  - 總計、可用和可用實體記憶體
  - 總計、可用和可用交換記憶體
  - CPU 的程式碼剖析捕獲，並以5秒的間隔處理 CPU 使用量
  - 進程記憶體使用量的分析捕獲（5秒間隔）

- 網路功能  
  - 具有介面卡統計資料的網路介面卡清單
  - 網路路由表
  - 開啟的埠和狀態

- 儲存體
  - 封鎖裝置清單
  - 資料分割清單
  - 掛接點清單
  - MDADM 磁片區資訊
  - LVM 磁片區資訊
  - 在每5秒間隔的所有磁片上進行程式碼剖析捕獲

- 記錄
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat 或/var/log/sa [ `**` ]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[擴充功能資料夾]/ \* 記錄檔\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - 過去五天 journalctl 的輸出

- [Azure 虛擬機器實例中繼資料](../windows/instance-metadata-service.md)

>[!Note]
>[ `*` ] 尚未在 Debian 和 SLES 散發套件上收集 PCI 資訊。
> 
>[ `**` ]/var/log/sysstat 或/var/log/sa 包含 sysstat 封裝所收集 (SAR) 檔案的系統活動報表。 如果 VM 上未安裝 sysstat 套件，PerfInsights 工具會提供安裝的建議。

## <a name="run-the-perfinsights-linux-on-your-vm"></a>在您的 VM 上執行 PerfInsights Linux

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>在執行此工具之前，我必須知道什麼？

#### <a name="tool-requirements"></a>工具需求

- 此工具必須在發生效能問題的 VM 上執行。
- 您必須在 VM 上安裝 python 3.x 或 Python 2.7。

- 目前支援下列發行版本：

    | 散發               | 版本                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux 伺服器        | 6.10 [ `*` ]、7.3、7.6、7。5 |
    | CentOS                     | 6.5 [ `*` ]、7。6                                    |
    | RHEL                       | 7.2、7.5、8.0 [ `*` ]                               |
    | Ubuntu                     | 14.04、16.04、18.04、20.04                               |
    | Debian                     | 8、9、10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] 請參閱 [已知問題](#known-issues) 一節

### <a name="known-issues"></a>已知問題

- RHEL 8 預設不會安裝 Python。 若要執行 PerfInsights Linux，您必須先安裝 Python 2。7

- CentOS 6.x 上的來賓代理程式資訊收集可能會失敗

- PCI 裝置資訊不會收集在以 Debian 為基礎的發行版本上

- 某些散發套件會部分收集 LVM 資訊

### <a name="how-do-i-run-perfinsights"></a>如何? 執行 PerfInsights

您可以從 Azure 入口網站安裝 Azure Performance Diagnostics，以在虛擬機器上執行 PerfInsights。 您也可以將它當作獨立的工具執行。

>[!Note]
>PerfInsights 只會收集和分析資料。 它不會對系統進行任何修改。

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>從 Azure 入口網站安裝及執行 PerfInsights

如需此選項的詳細資訊，請參閱 [Azure Performance Diagnostics](performance-diagnostics.md)。  

#### <a name="run-perfinsights-in-standalone-mode"></a>在獨立模式中執行 PerfInsights

若要執行 PerfInsights 工具，請遵循下列步驟：

1. 將 [PerfInsights](https://aka.ms/perfinsightslinuxdownload) 下載到虛擬機器上的資料夾，然後從終端機使用下列命令將內容解壓縮。

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. 流覽至包含檔案的資料夾 `perfinsights.py` ，然後執行 `perfinsights.py` 以查看可用的命令列參數。

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights Linux 命令列輸出的螢幕擷取畫面](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    執行 PerfInsights 案例的基本語法如下：

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    您可以使用下列範例來執行快速效能分析案例1分鐘，並在 [/tmp/output] 資料夾下建立結果：

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    您可以使用下列範例來執行5分鐘的效能分析案例，並將結果 tar 球上傳至儲存體帳戶：

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >執行案例之前，PerfInsights 會提示使用者同意共用診斷資訊並同意 EULA。 使用 **-a 或--接受-免責聲明** ----------------------
    >
    >如果您有與 Microsoft 搭配運作的主動式支援票證，並根據您正在處理的支援工程師要求執行 PerfInsights，請務必使用 **-s 或--支援要求** 選項來提供支援票證號碼。

當執行完成時，除非未指定輸出檔案夾，否則新的 tar 檔案會出現在與 PerfInsights 相同的資料夾中。 檔案的名稱是 **>performancediagnostics \_ yyyy-mm-dd \_ >hh-mm-ss-fff.zip. gz。** 您可以將此檔案傳送給支援專員進行分析，或在檔案內開啟報表，以查看結果和建議。

## <a name="review-the-diagnostics-report"></a>檢閱診斷報告

在 **>performancediagnostics \_ yyyy-mm-dd \_ >hh-mm-ss-fff.zip gz** 檔案中，您可以找到詳述 PerfInsights 結果的 HTML 報表。 若要審核報表，請展開 **>performancediagnostics \_ yyyy-mm-dd \_ >hh-mm-ss-fff.zip gz** 檔案，然後開啟 **PerfInsights Report.html** 檔案。

### <a name="overview-tab"></a>Overview (概觀) 索引標籤

[ **總覽** ] 索引標籤提供基本的執行詳細資料和虛擬機器資訊。 [ **結果** ] 索引標籤會顯示 PerfInsights 報表所有不同區段的建議摘要。

![PerfInsights 報表之 [總覽] 索引標籤的螢幕擷取畫面。](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![PerfInsights 報表之 [Linux] 索引標籤的螢幕擷取畫面。](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> 分類為「高」的結果是可能會造成效能問題的已知問題。 分類為「中」的結果表示不一定會造成效能問題的非最佳化設定。 分類為「低」的結果是僅用來參考的資訊說明。

檢閱所有「高」和「中」結果的建議和連結。 了解這些結果如何影響效能，以及效能最佳化組態的最佳做法。

### <a name="cpu-tab"></a>CPU 索引標籤

[ **CPU** ] 索引標籤提供在 PerfInsights 執行期間，全系統 CPU 耗用量的相關資訊。 高 CPU 使用率和最長時間執行的 CPU 取用者的相關資訊，有助於疑難排解 CPU 相關的高問題。

![PerfInsights [報表 CPU] 索引標籤的螢幕擷取畫面](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>[儲存體] 索引標籤

**結果**區段會顯示有關儲存體的各種結果與建議。

**封鎖裝置**和其他相關區段（例如**磁碟分割**、 **LVM**和**MDADM**索引標籤）說明如何設定和彼此相關的區塊裝置。

![[儲存體] 索引標籤的螢幕擷取畫面](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![[MDADM] 索引標籤的螢幕擷取畫面](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>[Linux] 索引標籤

[ **Linux** ] 索引標籤包含在 VM 中執行的硬體和作業系統的資訊。 詳細資料包括執行中的處理常式清單，以及有關來賓代理程式、PCI、CPU、驅動程式和 .LIS 驅動程式的資訊。

![[Linux] 索引標籤的螢幕擷取畫面](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>後續步驟

您可以將診斷記錄和報告上傳至 Microsoft 支援服務，以供進一步檢閱。 當您使用 Microsoft 支援服務員工時，他們可能會要求您傳輸 PerfInsights 所產生的輸出，以協助進行疑難排解程式。

下列螢幕擷取畫面會顯示類似您可能收到的一則訊息：

![來自 Microsoft 支援服務之範例訊息的螢幕擷取畫面](media/how-to-use-perfinsights-linux/support-email.png)

請依照訊息中的指示來存取檔案傳輸工作區。 為增加安全性，您必須在第一次使用時變更密碼。

登入之後，您會看到一個對話方塊，可上傳 PerfInsights 所收集的 **>performancediagnostics \_ yyyy-mm-dd \_ >hh-mm-ss-fff.zip gz** 檔案。
