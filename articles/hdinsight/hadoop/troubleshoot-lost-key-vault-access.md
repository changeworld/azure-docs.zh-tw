---
title: Azure HDInsight 叢集的磁片加密遺失 Key Vault 存取
description: 針對與 Azure HDInsight 叢集互動時的問題進行疑難排解的步驟和可能的解決方式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81461526"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>案例： Azure HDInsight 使用磁片加密的叢集遺失 Key Vault 存取

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

資源健康狀態中心（RHC）警示 `The HDInsight cluster is unable to access the key for BYOK encryption at rest` 會針對攜帶您自己的金鑰（BYOK）叢集顯示，其中叢集節點已失去客戶 Key Vault （KV）的存取權。 您也可以在 Apache Ambari UI 上看到類似的警示。

## <a name="cause"></a>原因

此警示可確保 KV 可從叢集節點存取，藉此確保使用者指派受控識別的網路連線、KV 健全狀況和存取原則。 此警示只是在後續節點重新開機時即將關閉代理程式的警告，叢集會繼續運作，直到節點重新開機為止。

流覽至 Apache Ambari UI，以從**磁片加密 Key Vault 狀態**尋找警示的詳細資訊。 此警示會有驗證失敗原因的詳細資料。

## <a name="resolution"></a>解決方案

### <a name="kvaad-outage"></a>KV/AAD 中斷

如需詳細資訊，請參閱[Azure Key Vault 可用性和冗余](../../key-vault/general/disaster-recovery-guidance.md)和 Azure 狀態頁面https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外刪除

* 將 KV 上的已刪除金鑰還原為自動復原。 如需詳細資訊，請參閱[復原已刪除的金鑰](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)。
* 與 KV 小組聯繫，以從意外刪除中復原。

### <a name="kv-access-policy-changed"></a>KV 存取原則已變更

針對指派給 HDI cluster 以存取 KV 的使用者指派受控識別，還原其存取原則。

### <a name="key-permitted-operations"></a>金鑰允許的作業

針對 KV 中的每個索引鍵，您可以選擇一組允許的作業。 請確定您已啟用 BYOK 金鑰的包裝和解除封裝作業

### <a name="expired-key"></a>過期的金鑰

如果已通過過期且金鑰未輪替，請從備份 HSM 或 contact KV 小組還原金鑰，以清除到期日。

### <a name="kv-firewall-blocking-access"></a>KV 防火牆封鎖存取

修正 KV 防火牆設定，以允許 BYOK 叢集節點存取 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>在虛擬網路上封鎖存取的 NSG 規則

檢查與連接至叢集的虛擬網路相關聯的 NSG 規則。

## <a name="mitigation-and-prevention-steps"></a>風險降低和預防步驟

### <a name="kv-accidental-deletion"></a>KV 意外刪除

* [設定具有資源鎖定集](../../azure-resource-manager/management/lock-resources.md)的 Key Vault。
* 將金鑰備份至其硬體安全性模組。

### <a name="key-deletion"></a>金鑰刪除

刪除金鑰之前，應該先刪除叢集。

### <a name="kv-access-policy-changed"></a>KV 存取原則已變更

定期審核並測試存取原則。

### <a name="expired-key"></a>過期的金鑰

* 將金鑰備份至您的 HSM。
* 使用沒有任何到期設定的金鑰。
* 如果需要設定到期日，請在到期日之前旋轉金鑰。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
