---
title: 使用 ID 代理（預覽）進行憑據管理 - Azure HDInsight
description: 瞭解 HDInsight ID 代理，以簡化域加入的 Apache Hadoop 群集的身份驗證。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483007"
---
# <a name="use-id-broker-preview-for-credential-management"></a>使用 ID 代理（預覽）進行憑據管理

本文介紹如何在 Azure HDInsight 中設置和使用 ID 代理功能。 您可以使用此功能通過 Azure 多重要素驗證登錄到 Apache Ambari，並獲取所需的 Kerberos 票證，而無需在 Azure 活動目錄域服務 （Azure AD DS） 中進行密碼雜湊。

## <a name="overview"></a>總覽

ID 代理在以下情況下簡化了複雜的身份驗證設置：

* 您的組織依靠識別身分同盟來驗證使用者訪問雲資源。 以前，要使用 HDInsight 企業安全包 （ESP） 群集，必須啟用從本地環境到 Azure 活動目錄的密碼雜湊同步。 對於某些組織來說，這一要求可能很困難或不可取。

* 您正在構建使用依賴于不同身份驗證機制的技術的解決方案。 例如，Apache Hadoop 和 Apache 遊俠依賴于 Kerberos，而 Azure 資料湖存儲依賴于 OAuth。

ID 代理提供統一的身份驗證基礎結構，並消除了將密碼雜湊同步到 Azure AD DS 的要求。 ID 代理由在 Windows 伺服器 VM（ID 代理節點）上運行的元件以及群集閘道節點組成。 

下圖顯示了啟用 ID Broker 後所有使用者（包括聯合使用者）的身份驗證流：

![使用 ID 代理的身份驗證流](./media/identity-broker/identity-broker-architecture.png)

ID 代理允許您使用多重要素驗證登錄到 ESP 群集，而無需提供任何密碼。 如果您已登錄到其他 Azure 服務（如 Azure 門戶），則可以使用單個登錄 （SSO） 體驗登錄到 HDInsight 群集。

## <a name="enable-hdinsight-id-broker"></a>啟用 HDInsight ID 代理

要創建啟用 ID 代理的 ESP 群集，請執行以下步驟：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 按照 ESP 群集的基本創建步驟操作。 有關詳細資訊，請參閱使用[ESP 創建 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。
1. 選擇**啟用 HDInsight ID 代理**。

ID 代理功能將添加一個額外的 VM 到群集。 此 VM 是 ID 代理節點，包括支援身份驗證的伺服器元件。 ID 代理節點是加入到 Azure AD DS 域的域。

![啟用 ID 代理的選項](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>工具集成

HDInsight [IntelliJ 外掛程式](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib)已更新以支援 OAuth。 您可以使用此外掛程式連接到群集並提交作業。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中沒有密碼雜湊的 SSH 訪問

啟用 ID 代理後，您仍然需要在 Azure AD DS 中存儲的密碼雜湊，用於具有域帳戶的 SSH 方案。 要 SSH 到加入域的 VM，或者要`kinit`運行該命令，您需要提供密碼。 

SSH 身份驗證要求雜湊在 Azure AD DS 中可用。 如果只想將 SSH 用於管理方案，則可以創建一個僅雲帳戶，並將其用於群集的 SSH。 其他使用者仍可以使用 Ambari 或 HDInsight 工具（如 IntelliJ 外掛程式），而無需在 Azure AD DS 中提供密碼雜湊。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 活動目錄域服務使用企業安全包配置 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [將 Azure Active Directory 使用者同步至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
* [監視叢集效能](../hdinsight-key-scenarios-to-monitor.md)
