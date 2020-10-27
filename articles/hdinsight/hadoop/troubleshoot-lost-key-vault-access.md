---
title: 具有磁片加密 Azure HDInsight 叢集遺失 Key Vault 存取權
description: 與 Azure HDInsight 叢集互動時，Key Vault 存取問題的疑難排解步驟和可能的解決方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: bc8162f3a7ca8744a94aba039996275b5f13c727
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533405"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>案例：具有磁片加密 Azure HDInsight 叢集遺失 Key Vault 存取權

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

資源健康狀態中心 (RHC) 警示 `The HDInsight cluster is unable to access the key for BYOK encryption at rest` 會顯示攜帶您自己的金鑰 (BYOK) 叢集，其中叢集節點會失去 Key Vault (KV) 的客戶存取權。 您也可以在 Apache Ambari UI 上看到類似的警示。

## <a name="cause"></a>原因

警示可確保 KV 可從叢集節點存取，藉此確保使用者指派的受控識別的網路連線、KV 健康情況和存取原則。 此警示只是在後續節點重新開機時即將關閉 broker 的警告，叢集會繼續運作，直到節點重新開機為止。

流覽至 Apache Ambari UI，以從 **磁片加密 Key Vault 狀態** 中尋找警示的詳細資訊。 此警示將會有有關驗證失敗原因的詳細資料。

## <a name="resolution"></a>解決方案

### <a name="kvaad-outage"></a>KV/AAD 中斷

查看 [Azure Key Vault 可用性和冗余](../../key-vault/general/disaster-recovery-guidance.md) 和 Azure 狀態頁面，以取得更多詳細資料 https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外刪除

* 在 KV 上還原已刪除的金鑰以自動復原。 如需詳細資訊，請參閱 [復原已刪除的金鑰](/rest/api/keyvault/recoverdeletedkey)。
* 與 KV 小組聯繫以復原意外刪除。

### <a name="kv-access-policy-changed"></a>KV 存取原則已變更

針對指派給 HDI 叢集以存取 KV 的使用者指派的受控識別，還原其存取原則。

### <a name="key-permitted-operations"></a>主要允許的作業

針對 KV 中的每個金鑰，您可以選擇允許的作業集合。 確定您已針對 BYOK 金鑰啟用包裝和解除包裝作業

### <a name="expired-key"></a>過期的金鑰

如果已超過到期且未輪替金鑰，請從備份 HSM 或 contact KV 小組還原金鑰，以清除到期日。

### <a name="kv-firewall-blocking-access"></a>KV 防火牆封鎖存取

修正 KV 防火牆設定，以允許 BYOK 叢集節點存取 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG 虛擬網路封鎖存取的規則

檢查與連接至叢集的虛擬網路相關聯的 NSG 規則。

## <a name="mitigation-and-prevention-steps"></a>緩和和預防步驟

### <a name="kv-accidental-deletion"></a>KV 意外刪除

* 使用 [資源鎖定集設定](../../azure-resource-manager/management/lock-resources.md)Key Vault。
* 將金鑰備份到其硬體安全性模組。

### <a name="key-deletion"></a>金鑰刪除

在刪除金鑰之前，應該先刪除叢集。

### <a name="kv-access-policy-changed"></a>KV 存取原則已變更

定期審核和測試存取原則。

### <a name="expired-key"></a>過期的金鑰

* 將金鑰備份到您的 HSM。
* 使用沒有任何過期設定的金鑰。
* 如果需要設定到期日，請在到期日之前輪替金鑰。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。