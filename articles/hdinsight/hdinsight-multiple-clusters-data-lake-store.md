---
title: '& 一個 Azure Data Lake Storage 帳戶的多個 HDInsight 叢集'
description: 了解如何透過單一 Data Lake Storage 帳戶使用多個 HDInsight 叢集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 19c40f2a7609d556448641e78fdeffe83e8660b1
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083945"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>透過一個 Azure Data Lake Storage 帳戶使用多個 HDInsight 叢集

從 HDInsight 3.5 版開始，您可以使用 Azure Data Lake Storage 帳戶來建立 HDInsight 叢集，以作為預設檔案系統。
Data Lake Storage 支援無限制的儲存體，使其不僅適合裝載大量資料，也適合裝載多個共用單一 Data Lake Storage 帳戶的 HDInsight 叢集。 如需如何使用 Data Lake Storage 作為儲存體來建立 HDInsight 叢集的指示，請參閱[快速入門：在 hdinsight 中設定](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)叢集。

本文提供建議給 Data Lake Storage 管理員來設定單一共用的 Data Lake Storage 帳戶，此帳戶可以跨多個**作用中的** HDInsight 叢集使用。 這些建議適用於將多個安全和不安全的 Apache Hadoop 叢集裝載於一個共用的 Data Lake Storage 帳戶上。

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage 檔案和資料夾等級 ACL

本文的其餘部分假設您已了解 Azure Data Lake Storage 中的檔案和資料夾等級 ACL，詳細說明請參閱 [Azure Data Lake Storage 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>適用於多個 HDInsight 叢集的 Data Lake Storage 設定

讓我們採用兩層的資料夾階層，說明使用多個 HDInsight 叢集搭配 Data Lake Storage 帳戶的建議。 假設您具有一個 Data Lake Storage 帳戶，而資料夾結構為 **/clusters/finance**。 在此結構中，財務組織所需的所有叢集都可以使用 /clusters/finance 作為儲存位置。 未來，如果有另一個組織 (假設是「行銷」) 想要使用同一個 Data Lake Storage 帳戶來建立 HDInsight 叢集，他們將會建立 /clusters/marketing。 現在，我們只要使用 **/clusters/finance** 即可。

為了讓 HDInsight 叢集有效使用此資料夾結構，Data Lake Storage 管理員必須指派適當的權限，如下表所述。 表格中顯示的權限對應至「存取 ACL」，而不是「預設 ACL」。

|資料夾  |權限  |擁有使用者  |擁有群組  | 具名使用者 | 具名使用者權限 | 具名群組 | 具名群組權限 |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |服務主體 |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |服務主體 |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |服務主體 |rwx  |-  |-     |

在表格中，

- **admin** 是 Data Lake Storage 帳戶的建立者和管理員。
- **服務主體**是與帳戶相關聯的 Azure Active Directory (AAD) 服務主體。
- **FINGRP** 是 AAD 中建立的使用者群組，內含來自財務組織的使用者。

如需有關如何建立 AAD 應用程式 (這也會建立服務主體) 的指示，請參閱[建立 AAD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 如需有關如何在 AAD 中建立使用者群組的指示，請參閱[在 Azure Active Directory 中管理群組](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

需要考慮的一些重要事項。

- 使用叢集的儲存體帳戶**之前**，Data Lake Storage 管理員必須使用適當權限來建立和佈建兩層的資料夾結構 (**/clusters/finance/**)。 建立叢集時，不會自動建立此結構。
- 上述範例建議將擁有群組 **/clusters/finance** 設為 **FINGRP**，並允許 **r-x** 讓 FINGRP 從根目錄開始存取整個資料夾階層。 這可確保 FINGRP 的成員可以從根目錄開始瀏覽資料夾結構。
- 當不同的 AAD 服務主體可以在 **/clusters/finance** 下建立叢集時，黏著位元 (在 **finance** 資料夾上設定時) 可確保一個服務主體所建立的資料夾無法被其他服務主體刪除。
- 一旦備妥資料夾結構和許可權，HDInsight 叢集建立程式就會在 **/clusters/finance/** 底下建立叢集特定的儲存位置。 例如，名稱為 fincluster01 之叢集的儲存體可能是 **/clusters/finance/fincluster01**。 下表顯示 HDInsight 叢集所建立之資料夾的擁有權和權限。

    |資料夾  |權限  |擁有使用者  |擁有群組  | 具名使用者 | 具名使用者權限 | 具名群組 | 具名群組權限 |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |服務主體 |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>作業輸入和輸出資料的相關建議

我們建議將作業的輸入資料和作業的輸出儲存在 **/clusters** 以外的資料夾。 這可確保即使刪除叢集特定資料夾來回收某些儲存空間，作業的輸入和輸出仍可供未來使用。 在這種情況下，請確定用來儲存作業輸入和輸出的資料夾層級，有提供適當的存取等級給服務主體。

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>共用單一儲存體帳戶的叢集所受的限制

可共用單一 Data Lake Storage 帳戶的叢集數目上限，取決於在那些叢集上執行的工作負載。 如果共用儲存體帳戶的叢集太多，或工作負載非常繁重，可能會導致對儲存體帳戶的輸入/輸出進行節流。

## <a name="support-for-default-acls"></a>預設 ACL 的支援

使用具名使用者存取權建立服務主體時 (如上表所示)，建議**不要**使用預設 ACL 來新增使用者。 如果使用預設 ACL 來佈建具名使用者存取權，將會導致將 770 權限指派給擁有使用者、擁有群組和其他人。 雖然此預設值770不會脫離擁有使用者（7）或擁有群組（7）的許可權，但它會移除其他人（0）的擁有權限。 這會在一個特定使用案例中產生已知問題，[已知問題和因應措施](#known-issues-and-workarounds)一節對此有詳細的討論。

## <a name="known-issues-and-workarounds"></a>已知問題和因應措施

本節列出透過 Data Lake Storage 使用 HDInsight 的已知問題及其因應措施。

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>公開可見的當地語系化 Apache Hadoop YARN 資源

建立新的 Azure Data Lake Storage 帳戶時，會自動佈建根目錄並將存取 ACL 權限位元設為 770。 根資料夾的擁有使用者會設定為建立帳戶的使用者 (Data Lake Storage 管理員)，而擁有群組會設定為建立帳戶之使用者的主要群組。 不會提供存取權給「其他人」。

已知這些設定會影響 [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247) 中記錄的一個特定 HDInsight 使用案例。 作業提交會失敗，錯誤訊息如下︰

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

如先前連結的 YARN JIRA 中所述，將公用資源當地語系化時，當地語系化人員會在遠端檔案系統上檢查所有要求之資源的權限，確保它們確實已公開。 任何不符合該條件的 LocalResource 都會遭到拒絕以進行當地語系化。 權限檢查包括「其他人」的檔案讀取權限。 在 Azure Data Lake 上裝載 HDInsight 叢集時，此案例不會有現成的作用，因為 Azure Data Lake 會拒絕根資料夾層級的「其他」存取權。

#### <a name="workaround"></a>因應措施

透過階層設定**其他人**的讀取和執行權限，例如在**/**、**/clusters** 和 **/clusters/finance**，如上表所示。

## <a name="see-also"></a>另請參閱

- [快速入門：在 HDInsight 中設定叢集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)
