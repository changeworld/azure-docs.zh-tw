---
title: hbase hbck 返回 Azure HDInsight 中的不一致
description: hbase hbck 返回 Azure HDInsight 中的不一致
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887320"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>方案：`hbase hbck`命令返回 Azure HDInsight 中的不一致

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue-region-is-not-in-hbasemeta"></a>問題：區域不在`hbase:meta`

HDFS 上的區域 xxx，但未`hbase:meta`在任何區域伺服器上列出或部署。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

1. 通過運行修復元表：

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 通過運行將區域分配到區域伺服器：

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>問題：區域處於離線狀態

區域 xxx 未部署在任何區域伺服器上。 這意味著該區域處於`hbase:meta`中，但處於離線狀態。

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

通過運列區域連線：

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>問題：區域具有相同的開始/結束鍵

### <a name="cause"></a>原因

變動。

### <a name="resolution"></a>解決方案

手動合併這些重疊區域。 轉到 HBase HMaster Web UI 表部分，選擇具有問題的錶鏈接。 您將看到屬於該表的每個區域的開始鍵/結束鍵。 然後合併這些重疊區域。 在 HBase 外殼`merge_region 'xxxxxxxx','yyyyyyy', true`中，執行 。 例如：

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

在這種情況下，您需要合併區域 A 和區域C，並獲取與區域 B 相同的鍵範圍的區域D，然後合併區域 B 和區域D。 xxxxxx 和 yyyyyyy 是每個區功能變數名稱稱末尾的雜湊字串。 請注意，不要合併兩個不連續的區域。 每次合併（如合併 A 和 C）後，HBase 將在區域 D 上啟動壓縮。 等待壓縮完成，然後再與區域D 進行另一次合併。 您可以在 HBase HMaster UI 中找到該區域伺服器頁上的壓縮狀態。

---

## <a name="issue-cant-load-regioninfo"></a>問題：無法載入`.regioninfo`

無法載入`.regioninfo`區域`/hbase/data/default/tablex/regiony`。

### <a name="cause"></a>原因

這很可能是由於區域伺服器崩潰或 VM 重新開機時區域部分刪除。 目前，Azure 存儲是一個平面 blob 檔案系統，某些檔操作不是原子的。

### <a name="resolution"></a>解決方案

手動清理這些剩餘的檔和資料夾：

1. 執行`hdfs dfs -ls /hbase/data/default/tablex/regiony`以檢查哪些資料夾/檔仍在其下方。

1. 執行`hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez`以刪除所有子檔/資料夾

1. 執行`hdfs dfs -rmr /hbase/data/default/tablex/regiony`以刪除區域資料夾。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
