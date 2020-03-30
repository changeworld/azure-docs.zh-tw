---
title: 如何在微軟 Azure 中使用 PerfInsights Linux*微軟文檔
description: 瞭解如何使用 PerfInsights 解決 Linux VM 性能問題。
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
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266984"
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload)是一種自助診斷工具，用於收集和分析診斷資料，並提供一個報告來説明解決 Azure 中的 Linux 虛擬機器性能問題。 PerfInsights 可以作為獨立工具在受支援的虛擬機器上運行，也可以直接通過[對 Azure 虛擬機器使用性能診斷](performance-diagnostics.md)從門戶運行。

如果您遇到虛擬機器的效能問題，在連絡支援人員之前，請先執行這個工具。

## <a name="supported-troubleshooting-scenarios"></a>支援的疑難排解案例

PerfInsights 可以收集並分析多種資訊。 下列幾節會說明常見案例。

### <a name="quick-performance-analysis"></a>快速效能分析

此方案收集基本資訊，如虛擬機器的存儲和硬體設定、各種日誌，包括：

- 作業系統資訊

- PCI 設備資訊

- 常規客體作業系統日誌

- 組態檔

- 儲存體資訊

- Azure 虛擬機器配置（使用[Azure 實例中繼資料服務](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)收集）

- 正在運行的進程、磁片、記憶體和 CPU 使用方式的清單

- 網路資訊

這是被動的資訊集合，應該不會影響系統。

>[!Note]
>快速性能分析方案自動包含在以下每個方案中：

### <a name="performance-analysis"></a>效能分析

此方案類似于快速性能分析，但允許捕獲診斷資訊的時間較長。

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights 收集了哪些類型的資訊

收集有關 Linux 虛擬機器、作業系統、塊設備、高資源消費者、配置和各種日誌的資訊。 以下是詳細資料：

- 作業系統
  - Linux 發行版本和版本
  - 內核資訊
  - 驅動程式資訊

- 硬體
  - PCI 設備`*`|

- 進程和記憶體
  - 進程清單（任務名稱、使用的記憶體、打開的檔）
  - 可用實體記憶體總數、可用記憶體和可用記憶體
  - 總計、可用和免費交換記憶體
  - 分析 CPU 捕獲，並在 5 秒間隔內處理 CPU 使用率
  - 在 5 秒間隔內分析進程記憶體使用方式的分析捕獲

- 網路  
  - 具有配接器統計資訊的網路介面卡清單
  - 網路路由表
  - 打開的埠和狀態

- 存放裝置
  - 阻止設備清單
  - 資料分割清單
  - 裝載點清單
  - MDADM 卷資訊
  - LVM 卷資訊
  - 在 5 秒間隔內對所有磁片進行分析捕獲

- 記錄
  - /var/log/messages
  - /var/日誌/系統日誌
  - /var/日誌/克爾恩.log
  - /var/日誌/cron.log
  - /var/日誌/引導.log
  - /var/日誌/yum.log
  - /var/日誌/dpkg.log
  - /var/日誌/雲 init.log
  - /var/日誌/雲-以輸出。日誌
  - /var/日誌/gpu 管理器.log
  - /var/log/waagent.log
  - /var/日誌/azure/[擴展資料夾]/\*日誌\*
  - /var/選擇/微軟/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /等/花
  - 過去五天的日誌輸出

- [Azure 虛擬機器實例中繼資料](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] 尚未收集關於 Debian 和 SLES 分佈的 PCI 資訊

## <a name="run-the-perfinsights-linux-on-your-vm"></a>在 VM 上運行 PerfInsights Linux

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>在運行該工具之前，我必須瞭解哪些資訊

#### <a name="tool-requirements"></a>工具需求

- 此工具必須在發生效能問題的 VM 上執行。
- Python 2.7 必須安裝在 VM 上

- 當前支援以下分發：

    | 散發               | 版本                                         |
    |----------------------------|-------------------------------------------------|
    | 甲骨文Linux伺服器        | 6.10`*`= 、7.3、7.6、7.5（Oracle-資料庫-Ee 13.8 市場圖像）|
    | CentOS                     | 6.5`*`[ ， 7.6                                    |
    | RHEL                       | 7.2， 7.5， 8.0 |`*`                               |
    | Ubuntu                     | 14.04、16.04、18.04                               |
    | Debian                     | 8、 9、 10 |`*`                                    |
    | SLES                       | 12 SP4`*`|                                      |
    |                            |                                                   |

>[!Note]
>[`*`] 請參閱[已知問題](#known-issues)部分

### <a name="known-issues"></a>已知問題

- 預設情況下，RHEL 8 未安裝 Python。 要運行 PerfInsights Linux，您必須首先安裝 Python 2.7

- 來賓代理資訊收集可能在 CentOS 6.x 上失敗

- PCI 設備資訊不會在基於 Debian 的分發上收集

- LVM 資訊在某些發行版本上部分收集

### <a name="how-do-i-run-perfinsights"></a>如何運行 PerfInsights

您可以通過從 Azure 門戶安裝 Azure 性能診斷，在虛擬機器上運行 PerfInsights。 您也可以將它當作獨立的工具執行。

>[!Note]
>PerfInsights 只需收集和分析資料。 它不對系統進行任何修改。

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>從 Azure 入口網站安裝及執行 PerfInsights

有關此選項的詳細資訊，請參閱 Azure[性能診斷](performance-diagnostics.md)。  

#### <a name="run-perfinsights-in-standalone-mode"></a>在獨立模式中執行 PerfInsights

若要執行 PerfInsights 工具，請遵循下列步驟：

1. 將[PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload)下載到虛擬機器上的資料夾，並使用以下命令從終端中提取內容。

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. 導航到包含`perfinsights.py`檔的資料夾，然後運行`perfinsights.py`以查看可用的命令列參數。

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights Linux 命令線輸出的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    執行 PerfInsights 案例的基本語法如下：

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    可以使用以下示例運行 1 分鐘的快速性能分析方案，並在 /tmp/輸出檔案夾下創建結果：

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    您可以使用以下示例運行性能分析方案 5 分鐘，並將結果 tar 球上載到存儲帳戶：

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >執行案例之前，PerfInsights 會提示使用者同意共用診斷資訊並同意 EULA。 使用 **-a 或 -- 接受免責聲明和共用診斷**選項跳過這些提示。
    >
    >如果您有 Microsoft 的有效支援票證，並且根據您正在使用的支援工程師的請求運行 PerfInsights，請確保使用 **-s 或 --支援請求**選項提供支援票證編號。

運行完成後，除非未指定輸出檔案夾，否則新的 tar 檔將顯示在與 PerfInsights 相同的資料夾中。 檔的名稱是**性能診斷\_\_yyyy-MM-dd hh-mm-s-fff.tar.gz。** 您可以將此檔發送給支援代理進行分析，或在檔內打開報表以查看調查結果和建議。

## <a name="review-the-diagnostics-report"></a>檢閱診斷報告

在**性能診斷\_yyyy-MM-dd\_hh-mm-s-fff.tar.gz**檔中，您可以找到一個 HTML 報告，詳細說明 PerfInsights 的調查結果。 要查看報告，可以展開**性能診斷\_yyyy-MM-dd\_hh-mm-s-fff.tar.gz**檔，然後打開**PerfInsights Report.html**檔。

### <a name="overview-tab"></a>Overview (概觀) 索引標籤

**"概述"** 選項卡提供基本運行詳細資訊和虛擬機器資訊。 "**調查結果"** 選項卡顯示 PerfInsights 報告所有不同部分的建議摘要。

![PerfInsights 報告的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![PerfInsights 報告的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> 分類為「高」的結果是可能會造成效能問題的已知問題。 分類為「中」的結果表示不一定會造成效能問題的非最佳化設定。 分類為「低」的結果是僅用來參考的資訊說明。

檢閱所有「高」和「中」結果的建議和連結。 了解這些結果如何影響效能，以及效能最佳化組態的最佳做法。

### <a name="cpu-tab"></a>CPU 選項卡

**CPU**選項卡提供有關 PerfInsights 運行期間系統範圍 CPU 限定的資訊。 有關 CPU 使用期高和執行時間長的 CPU 消費者的資訊將有助於解決與 CPU 相關的高問題。

![PerfInsights 報告 CPU 選項卡的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>[儲存體] 索引標籤

**結果**區段會顯示有關儲存體的各種結果與建議。

**塊設備**和其他相關部分（如**分區****、LVM**和**MDADM**選項卡）描述塊設備的配置方式和相互關聯。

![存儲選項卡的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![MDADM 選項卡的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux 選項卡

**Linux**選項卡包含有關在 VM 中運行的硬體和作業系統的資訊。 詳細資訊包括正在運行的進程的清單以及有關來賓代理、PCI、CPU、驅動程式和 LIS 驅動程式的資訊。

![Linux 選項卡的螢幕截圖](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>後續步驟

您可以將診斷記錄和報告上傳至 Microsoft 支援服務，以供進一步檢閱。 當您與 Microsoft 支援人員合作時，他們可能會要求您傳輸 PerfInsights 生成的輸出，以協助故障排除過程。

下列螢幕擷取畫面會顯示類似您可能收到的一則訊息：

![來自 Microsoft 支援服務之範例訊息的螢幕擷取畫面](media/how-to-use-perfinsights-linux/support-email.png)

請依照訊息中的指示來存取檔案傳輸工作區。 為增加安全性，您必須在第一次使用時變更密碼。

登錄後，您將找到一個對話方塊來上傳 PerfInsights 收集的**性能診斷\_\_yyyy-MM-dd hh-mm-sf.tar.gz**檔。
