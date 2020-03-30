---
title: Azure HDInsight 中的託管標識
description: 概述了 Azure HDInsight 中託管標識的實現情況。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206005"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的託管標識

託管標識是在 Azure 活動目錄 （Azure AD） 中註冊的標識，其憑據由 Azure 管理。 使用託管標識時，無需在 Azure AD 中註冊服務主體或維護憑據（如證書）。

Azure HDInsight 中託管標識用於訪問 Azure AD 域服務或在需要時訪問 Azure 資料湖存儲 Gen2 中的檔。

有兩種類型的託管標識：使用者分配和系統分配。 Azure HDInsight 僅支援使用者分配的託管標識。 HDInsight 不支援系統分配的託管標識。 使用者分配的託管標識創建為獨立的 Azure 資源，然後您可以將其分配給一個或多個 Azure 服務實例。 相反，在 Azure AD 中創建系統分配的託管標識，然後直接在特定 Azure 服務實例上啟用。 然後，系統分配的託管標識的生命週期與啟用該服務實例的生命週期相關聯。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 託管身份實現

在 Azure HDInsight 中，託管標識在群集的每個節點上預配。 但是，這些標識元件僅供 HDInsight 服務使用。 當前沒有支援使用 HDInsight 叢集節點上安裝的託管標識生成訪問權杖的方法。 對於某些 Azure 服務，託管標識使用終結點實現，可用於獲取訪問權杖以自行與其他 Azure 服務進行交互。

## <a name="create-a-managed-identity"></a>創建託管標識

可以使用以下任何方法創建託管標識：

* [Azure 門戶](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure 電源外殼](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 資源管理器](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

配置託管標識的剩餘步驟取決於將使用該標識的方案。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的託管標識方案

託管標識在 Azure HDInsight 中用於多種方案。 有關詳細的設置和配置說明，請參閱相關文檔：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [企業安全包](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [客戶管理金鑰磁碟加密](disk-encryption.md)

## <a name="faq"></a>常見問題集
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>如果在群集創建後刪除託管標識，會發生什麼情況？
當需要託管標識時，群集將遇到問題。 創建群集後，當前無法更新或更改管理標識。 因此，我們的建議是確保託管標識在群集運行時不會被刪除。 或者，您可以重新創建群集並分配新的託管標識。

## <a name="next-steps"></a>後續步驟

* [什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
