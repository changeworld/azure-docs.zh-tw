---
title: Azure HDInsight 中的受控識別
description: 概述如何在 Azure HDInsight 中執行受控識別。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 098c0a85dc6c0fac8b78f344c4c8559b168b9114
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371332"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的受控識別

受控識別是在 Azure Active Directory (Azure AD) 的身分識別，其認證是由 Azure 管理。 使用受控識別時，您不需要在 Azure AD 中註冊服務主體。 或維護憑證之類的認證。

受控識別會在 Azure HDInsight 中用來存取 Azure Data Lake Storage Gen2 中的 Azure AD 網域服務或存取檔案（如有需要）。

受控識別有兩種類型：使用者指派和系統指派。 Azure HDInsight 僅支援使用者指派的受控識別。 HDInsight 不支援系統指派的受控識別。 使用者指派的受控識別會建立為獨立的 Azure 資源，您接著可以將其指派給一或多個 Azure 服務實例。 相反地，系統指派的受控識別會在 Azure AD 中建立，然後直接在特定的 Azure 服務實例上啟用。 系統指派的受控識別的存留期，接著會系結至已啟用它的服務實例存留期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 受控識別執行

在 Azure HDInsight 中，受控識別只能由適用于內部元件的 HDInsight 服務使用。 目前沒有支援的方法可以使用安裝在 HDInsight 叢集節點上的受控識別來產生存取權杖，以存取外部服務。 針對某些 Azure 服務（例如計算 Vm），受控識別會以您可用來取得存取權杖的端點來執行。 此端點目前無法在 HDInsight 節點中使用。

如果您需要啟動應用程式以避免將秘密/密碼放在分析作業中 (例如 SCALA 作業) ，您可以使用腳本動作，將您自己的憑證散發到叢集節點，然後使用該憑證來取得存取權杖 (例如存取 Azure KeyVault) 。

## <a name="create-a-managed-identity"></a>建立受控識別

您可以使用下列任何一種方法來建立受控識別：

* [Azure 入口網站](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

設定受控識別的其餘步驟取決於其將使用的案例。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的受控識別案例

受控識別會在多個案例的 Azure HDInsight 中使用。 請參閱相關檔以取得詳細的安裝和設定指示：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity) \(部分機器翻譯\)
* [企業安全性套件](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [客戶管理的金鑰磁碟加密](disk-encryption.md)

HDInsight 會自動更新您在這些案例中使用之受控識別的憑證。 不過，當長時間執行的叢集使用多個不同的受控識別時，有一項限制，憑證更新可能無法如預期般針對所有受控識別運作。 由於這項限制，如果您打算使用長時間執行的叢集 (例如超過60天的) ，我們建議您在上述所有案例中使用相同的受控識別。 

如果您已經使用多個不同的受控識別來建立長時間執行的叢集，而且遇到下列其中一個問題：
 * 在 ESP 叢集中，叢集服務會開始失敗或擴大，而其他作業則會因為驗證錯誤而失敗。
 * 在 ESP 叢集中，變更 AAD DS LDAPS 憑證時，LDAPS 憑證不會自動更新，因此 LDAP 同步和擴展 ups 會開始失敗。
 * ADLS Gen2 的 MSI 存取開始失敗。
 * 在 CMK 案例中無法輪替加密金鑰。
然後，您應該將上述案例所需的角色和許可權指派給叢集中使用的所有受控識別。 例如，如果您針對 ADLS Gen2 和 ESP 叢集使用不同的受控識別，則兩者都應該將「儲存體 blob 資料擁有者」和「HDInsight 網域服務參與者」角色指派給他們，以避免在這些問題中遇到。

## <a name="faq"></a>常見問題集

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>如果在叢集建立之後刪除受控識別，會發生什麼事？

當需要受控識別時，您的叢集會遇到問題。 目前沒有任何方法可在叢集建立之後更新或變更受控識別。 因此我們的建議是確保在叢集執行時間不會刪除受控識別。 或者，您可以重新建立叢集，並指派新的受控識別。

## <a name="next-steps"></a>後續步驟

* [什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
