---
title: 具有磁碟加密的 Azure HDInsight 叢集失去金鑰保管庫存取權限
description: 在與 Azure HDInsight 群集互動時,對問題的步驟和可能的解決方案進行故障排除。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461526"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>方案:具有磁碟加密的 Azure HDInsight 叢集失去金鑰保管庫存取權限

本文介紹了與 Azure HDInsight 群集互動時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

資源運行狀況中心 (RHC)`The HDInsight cluster is unable to access the key for BYOK encryption at rest`警報 顯示,用於自帶密鑰 (BYOK) 群集,其中群集節點已失去對客戶密鑰保管庫 (KV) 的訪問。 類似的警報也可以看到阿帕契巴里 UI。

## <a name="cause"></a>原因

警報可確保 KV 可從群集節點存取,從而確保使用者分配的託管標識的網路連接、KV 運行狀況和存取策略。 此警報只是後續節點重新啟動時即將關閉代理的警告,群集將繼續運行,直到節點重新啟動。

導航到 Apache Ambari UI,從**磁碟加密密鑰保管庫狀態**中查找有關警報的詳細資訊。 此警報將提供有關驗證失敗原因的詳細資訊。

## <a name="resolution"></a>解決方案

### <a name="kvaad-outage"></a>KV/AAD 中斷

有關詳細資訊,請查看[Azure 金鑰保管庫可用性和冗餘以及](../../key-vault/general/disaster-recovery-guidance.md)Azure 狀態頁https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外移除

* 將 KV 上已刪除的金鑰還原為自動復原。 關於詳細資訊,請參閱[回復已刪除的金鑰](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)。
* 聯繫 KV 團隊,從意外刪除中恢復。

### <a name="kv-access-policy-changed"></a>KV 存取原則已變更

還原分配給 HDI 群集以訪問 KV 的使用者分配的託管標識的訪問策略。

### <a name="key-permitted-operations"></a>金鑰允許的操作

對於 KV 中的每個鍵,您可以選擇允許的操作集。 確保為 BYOK 金鑰啟用了換行和解套件操作

### <a name="expired-key"></a>過期金鑰

如果過期已過且密鑰未旋轉,請從備份 HSM 還原密鑰或聯繫 KV 團隊以清除到期日期。

### <a name="kv-firewall-blocking-access"></a>KV 防火牆封鎖存取

修復 KV 防火牆設定,允許 BYOK 群集節點存取 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>關於虛擬網路封鎖存取的 NSG 規則

檢查與連接到群集的虛擬網路關聯的 NSG 規則。

## <a name="mitigation-and-prevention-steps"></a>緩解和預防步驟

### <a name="kv-accidental-deletion"></a>KV 意外移除

* 使用[資源鎖定集](../../azure-resource-manager/management/lock-resources.md)配置金鑰保管庫。
* 將密鑰備份到其硬體安全模組。

### <a name="key-deletion"></a>金鑰刪除

在刪除金鑰之前,應刪除群集。

### <a name="kv-access-policy-changed"></a>KV 存取原則已變更

定期審核和測試訪問策略。

### <a name="expired-key"></a>過期金鑰

* 將金鑰備份到HSM。
* 使用不設置任何過期集的密鑰。
* 如果需要設置過期,請在到期日期之前旋轉鍵。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源:答案、支持和專家。

* 如果需要更多説明,可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從選單列中選擇 **「支援」** 或打開 **「説明 + 支援**中心」。 關於詳細資訊,請檢視[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問,並且通過[Azure 支援計劃](https://azure.microsoft.com/support/plans/)之一提供技術支援。
