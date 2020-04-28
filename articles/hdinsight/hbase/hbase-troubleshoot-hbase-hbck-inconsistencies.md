---
title: hbase hbck 在 Azure HDInsight 中傳回不一致的情況
description: hbase hbck 在 Azure HDInsight 中傳回不一致的情況
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887320"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>案例： `hbase hbck`命令在 Azure HDInsight 中傳回不一致的情況

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue-region-is-not-in-hbasemeta"></a>問題：區域不在`hbase:meta`

HDFS 上的 Region xxx，但未列`hbase:meta`于或部署于任何區域伺服器上。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方法

1. 執行下列程式來修正中繼資料表：

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 執行下列程式，將區域指派給 RegionServers：

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>問題：區域已離線

未在任何 RegionServer 上部署 Region xxx。 這表示區域在中`hbase:meta`，但離線。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方法

執行下列程式，讓區域上線：

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>問題：區域具有相同的開始/結束金鑰

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方法

手動合併那些重迭的區域。 移至 [HBase HMaster Web UI 資料表] 區段中，選取具有問題的 [資料表] 連結。 您會看到屬於該資料表的每個區域的「開始」索引鍵/結束金鑰。 然後合併這些重迭的區域。 在 HBase shell 中， `merge_region 'xxxxxxxx','yyyyyyy', true`執行。 例如：

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

在此案例中，您需要合併 RegionA 和 RegionC，並取得與 RegionB 相同的索引鍵範圍的 RegionD，然後合併 RegionB 和 RegionD。 xxxxxxx 和 yyyyyy 是每個區功能變數名稱稱結尾的雜湊字串。 請小心不要合併兩個不連續的區域。 在每次合併之後（例如 merge A 和 C），HBase 會在 RegionD 上啟動壓縮。 等待壓縮完成，再使用 RegionD 進行另一個合併。 您可以在 HBase HMaster UI 中的該區域伺服器頁面上找到壓縮狀態。

---

## <a name="issue-cant-load-regioninfo"></a>問題：無法載入`.regioninfo`

無法為`.regioninfo`區域`/hbase/data/default/tablex/regiony`載入。

### <a name="cause"></a>原因

這很可能是因為在 RegionServer 損毀或 VM 重新開機時，區域部分刪除。 目前，Azure 儲存體是一般 blob 檔案系統，有些檔案作業則不是不可部分完成的。

### <a name="resolution"></a>解決方法

手動清除這些剩餘的檔案和資料夾：

1. 執行`hdfs dfs -ls /hbase/data/default/tablex/regiony`以檢查仍在其下的資料夾/檔案。

1. 執行`hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez`以刪除所有的子檔案/資料夾

1. 執行`hdfs dfs -rmr /hbase/data/default/tablex/regiony`以刪除區域資料夾。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
