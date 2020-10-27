---
title: hbase hbck 會在 Azure HDInsight 中傳回不一致的情況
description: hbase hbck 會在 Azure HDInsight 中傳回不一致的情況
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a3f6a709d3e0f1c71f89473b6ed8435c6eea7e1c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540222"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>案例： `hbase hbck` 命令會傳回 Azure HDInsight 中的不一致

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue-region-is-not-in-hbasemeta"></a>問題：區域不在 `hbase:meta`

HDFS 上的區域 xxx，但未列于 `hbase:meta` 或部署在任何區域伺服器上。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

1. 藉由執行下列各項來修正中繼資料表：

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 藉由執行下列動作，將區域指派給 RegionServers：

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>問題：區域處於離線狀態

未在任何 RegionServer 上部署 Region xxx。 這表示區域處於 `hbase:meta` （但離線）。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

執行下列各項使區域上線：

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>問題：區域具有相同的開始/結束金鑰

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

手動合併這些重迭區域。 移至 HBase HMaster Web UI 資料表區段，選取有問題的資料表連結。 您將會看到屬於該資料表之每個區域的啟動金鑰/結束金鑰。 然後合併這些重迭區域。 在 HBase shell 中，請進行 `merge_region 'xxxxxxxx','yyyyyyy', true` 。 例如：

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

在此案例中，您必須合併 RegionA 和 RegionC，並取得具有與 RegionB 相同索引鍵範圍的 RegionD，然後合併 RegionB 和 RegionD。 >xxxxxxx 和 yyyyyy 是每個區功能變數名稱稱結尾的雜湊字串。 請小心，不要合併兩個不連續的區域。 在每個合併（例如 merge A 和 C）之後，HBase 會在 RegionD 上開始壓縮。 先等待壓縮完成，再使用 RegionD 進行另一個合併。 您可以在 HBase HMaster UI 中的 [區域伺服器] 頁面上找到壓縮狀態。

---

## <a name="issue-cant-load-regioninfo"></a>問題：無法載入 `.regioninfo`

無法載入 `.regioninfo` 區域 `/hbase/data/default/tablex/regiony` 。

### <a name="cause"></a>原因

這很可能是因為在 RegionServer 損毀或 VM 重新開機時，區域部分刪除的緣故。 目前，Azure 儲存體是一般 blob 檔案系統，某些檔案作業不是不可部分完成的。

### <a name="resolution"></a>解決方案

手動清除這些剩餘的檔案和資料夾：

1. 執行 `hdfs dfs -ls /hbase/data/default/tablex/regiony` 以檢查仍在其下的資料夾/檔案。

1. 執行 `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` 以刪除所有子檔案/資料夾

1. 執行 `hdfs dfs -rmr /hbase/data/default/tablex/regiony` 以刪除區域資料夾。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。