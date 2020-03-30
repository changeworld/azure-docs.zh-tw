---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 獲取 Hadoop、Spark、R 伺服器、蜂巢等的開發提示和詳細資訊。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e5a96d2eb67937ce4eeaa1999d8168e7f5d3d926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130196"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>總結

Azure HDInsight 是企業客戶中最受歡迎的 Azure 開源分析服務之一。

## <a name="release-date-01092020"></a>發佈日期： 01/09/2020

此版本適用于 HDInsight 3.6 和 4.0。 HDInsight 版本可在幾天內提供給所有區域。 此處的發佈日期表示第一個區域發佈日期。 如果您沒有看到以下更改，請等待該版本在幾天內在您所在的地區中使用。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。

## <a name="new-features"></a>新功能
### <a name="tls-12-enforcement"></a>TLS 1.2 強制執行
傳輸層安全性 (TLS) 和安全通訊端層 (SSL) 是密碼編譯通訊協定，可透過電腦網路提供通訊安全性。 瞭解有關[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)的更多。 HDInsight 在公共 TSP 終結點上使用 TLS 1.2，但 TLS 1.1 仍支援向後相容性。 

使用此版本，客戶只能選擇通過公共群集終結點的所有連接加入 TLS 1.2。 為此，引入了新屬性**minSupportTlsVersion，** 可以在群集創建期間指定。 如果未設置該屬性，群集仍支援 TLS 1.0、1.1 和 1.2，這與今天的行為相同。 客戶可以將此屬性的值設置為"1.2"，這意味著群集僅支援 TLS 1.2 及以上。 有關詳細資訊，請參閱[規劃虛擬網路 - 傳輸層安全性](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)。

### <a name="bring-your-own-key-for-disk-encryption"></a>自帶金鑰進行磁片加密
HDInsight 中的所有受控磁碟都會使用 Azure 儲存體服務加密 (SSE) 來加以保護。 預設情況下，這些磁片上的資料由 Microsoft 管理的金鑰加密。 從此版本開始，您可以自帶金鑰 （BYOK） 進行磁片加密，並使用 Azure 金鑰保存庫對其進行管理。 BYOK 加密是在群集創建期間進行的單步配置，無需額外費用。 只需將 HDInsight 註冊為 Azure 金鑰保存庫的託管標識，並在創建群集時添加加密金鑰。 有關詳細資訊，請參閱[客戶管理金鑰磁片加密](https://docs.microsoft.com/azure/hdinsight/disk-encryption)。

## <a name="deprecation"></a>淘汰
此版本沒有棄用。 要為即將發生的棄用做好準備，請參閱[即將進行的更改](#upcoming-changes)。

## <a name="behavior-changes"></a>行為變更
此版本的行為未更改。 要為即將發生的更改做好準備，請參閱[即將發生的更改](#upcoming-changes)。

## <a name="upcoming-changes"></a>即將推出的變更
在即將發佈的版本中，將發生以下更改。 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>頭節點至少需要 4 核 VM 
頭節點至少需要 4 核 VM，以確保 HDInsight 群集的高可用性和可靠性。 從 2020 年 4 月 6 日起，客戶只能選擇 4 核或以上 VM 作為新 HDInsight 群集的頭節點。 現有群集將繼續按預期運行。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP 火花叢集節點大小更改 
在即將發佈的版本中，ESP Spark 群集的最小允許節點大小將更改為Standard_D13_V2。 由於 CPU 和記憶體容量相對較低，A 系列 VM 可能會導致 ESP 群集問題。 A 系列 VM 將被棄用以創建新的 ESP 群集。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移動到 Azure 虛擬機器縮放集
HDInsight 現在使用 Azure 虛擬機器來預配群集。 在即將發佈的版本中，HDInsight 將改用 Azure 虛擬機器縮放集。 查看有關 Azure 虛擬機器縮放集的更多。

### <a name="hbase-20-to-21"></a>HBase 2.0 到 2.1
在即將推出的 HDInsight 4.0 版本中，HBase 版本將從版本 2.0 升級到 2.1。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 不斷改進群集可靠性和性能。 

## <a name="component-version-change"></a>元件版本更改
此版本的元件版本未更改。 您可以在此處找到 HDInsight 4.0 廣告 HDInsight 3.6 的當前元件版本。

## <a name="known-issues"></a>已知問題

截至 2020 年 3 月 18 日，西歐或北歐的一些 Azure HDInsight 客戶在這些地區創建或擴展 HDInsight 群集時收到錯誤通知。 與此問題相關的錯誤包括：

- 處理請求時發生內部伺服器錯誤。 請重試請求或聯繫支援人員。
- 至少有一個資源部署操作失敗。 有關詳細資訊，請列出部署操作。 有關使用https://aka.ms/DeployOperations詳情，請參閱
- 使用者訂閱 Id\<'\>訂閱 ID ' 沒有內核用於創建\<資源"群集名稱>"。 要求： \< \>X ， 可用： 0.

工程師們意識到了這個問題，並正在積極調查。

有關其他説明，請創建[支援請求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
