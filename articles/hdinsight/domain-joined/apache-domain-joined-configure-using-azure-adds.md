---
title: 使用 Azure AD DS 的企業安全性 - Azure HDInsight
description: 瞭解如何使用 Azure 活動目錄域服務設置和配置 HDInsight 企業安全包群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272834"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>HDInsight 中具有 Azure 活動目錄域服務的企業安全包配置

企業安全包 （ESP） 群集在 Azure HDInsight 群集上提供多使用者訪問。 具有 ESP 的 HDInsight 叢集會與網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。

在本文中，您將瞭解如何使用 Azure 活動目錄域服務 （Azure AD DS） 使用 ESP 配置 HDInsight 群集。

> [!NOTE]  
> ESP 通常可用於 HDInsight 3.6 和 4.0 中，適用于這些群集類型：Apache Spark、互動式、Hadoop 和 HBase。 Apache Kafka 群集類型的 ESP 處於預覽狀態，僅提供盡力支援。 不支援在 ESP GA 日期（2018 年 10 月 1 日之前創建的 ESP 群集）。

## <a name="enable-azure-ad-ds"></a>啟用 Azure AD DS

> [!NOTE]  
> 只有租戶管理員才具有啟用 Azure AD DS 的許可權。 如果群集存儲是 Azure 資料存儲第 1 代或第 2 代，則僅對需要使用基本 Kerberos 身份驗證訪問群集的使用者禁用 Azure 多重要素驗證。 
>
> *僅當*特定使用者訪問 HDInsight 群集虛擬網路的 IP 範圍時，才能使用[受信任的 IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips)或[條件訪問](../../active-directory/conditional-access/overview.md)來禁用多因素身份驗證。 如果使用條件訪問，請確保 HDInsight 虛擬網路上的 Active Directory 服務終結點已啟用。
>
> 如果群集存儲是 Azure Blob 存儲，則不要禁用多重要素驗證。

啟用 Azure AD DS 是使用 ESP 創建 HDInsight 群集的先決條件。 有關詳細資訊，請參閱使用[Azure 門戶啟用 Azure 活動目錄域服務](../../active-directory-domain-services/tutorial-create-instance.md)。 

啟用 Azure AD DS 後，預設情況下，所有使用者和物件都開始從 Azure 活動目錄 （Azure AD） 同步到 Azure AD DS。 同步作業的所需時間取決於 Azure AD 中的物件數目。 對於數十萬個物件，同步可能需要幾天時間。 

與 Azure AD DS 一起使用的功能變數名稱必須為 39 個字元或更少，才能使用 HDInsight。

您可以選擇僅同步需要訪問 HDInsight 群集的組。 這個僅同步特定群組的選項，稱為*特定範圍同步處理*。 有關說明，請參閱[將範圍化同步從 Azure AD 配置到託管域](../../active-directory-domain-services/scoped-synchronization.md)。

啟用安全 LDAP 時，將功能變數名稱放在主題名稱和證書中的主題替代名稱中。 例如，如果您的網域名稱是 *contoso100.onmicrosoft.com*，請確定相同的名稱存在於您的憑證主體名稱與主體替代名稱中。 如需詳細資訊，請參閱[針對 Azure AD DS 受控網域設定安全的 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。 

下面的示例創建一個自簽章憑證。 功能變數名稱*contoso100.onmicrosoft.com*在（主題名稱）`Subject`和`DnsName`（主題替代名稱）中。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>檢查 Azure AD DS 運行狀況狀態
通過在 **"管理"** 類別中選擇**運行狀況**，查看 Azure 活動目錄域服務的運行狀況。 確保 Azure AD DS 的狀態為綠色（正在運行），並且同步已完成。

![Azure AD DS 運行狀況](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>創建和授權託管標識

您可以使用*使用者分配的託管標識*來簡化和説明保護域服務操作。 將**HDInsight 域服務參與者**角色指派給託管標識時，它可以讀取、創建、修改和刪除域服務操作。 

HDInsight 企業安全包需要某些域服務操作，如創建 OEM 和服務主體。 您可以在任何訂閱中創建託管標識。 有關託管標識的詳細資訊，請參閱[Azure 資源的託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。 有關託管標識在 Azure HDInsight 中的工作方式的詳細資訊，請參閱[Azure HDInsight 中的託管標識](../hdinsight-managed-identities.md)。

要設置 ESP 群集，請創建使用者分配的託管標識（如果尚未創建）。 有關說明，請參閱[使用 Azure 門戶創建、列出、刪除或將角色指派給使用者分配的託管標識](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

接下來，將**HDInsight 域服務參與者**角色指派給 Azure AD DS**訪問控制項**中的託管標識。 您需要 Azure AD DS 管理員許可權才能分配此角色。

![Azure Active Directory Domain Services 存取控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

分配**HDInsight 域服務參與者**角色可確保此標識具有對 Azure AD DS 域執行域服務操作的適當（代表）存取權限。 這些操作包括創建和刪除 O。

創建託管標識並給出正確的角色後，Azure AD DS 管理員可以設置誰可以使用此託管標識。 首先，管理員在門戶中選擇託管標識，然後在 **"概述**"下選擇**存取控制 （IAM）。** 然後，在右側，管理員將**託管標識操作員**角色指派給希望創建 HDInsight ESP 群集的使用者或組。 

例如，Azure AD DS 管理員可以將此角色指派給**sjmsi**託管標識**的市場行銷團隊**組，如下圖所示。 此分配可確保組織中的正確人員可以使用託管標識創建 ESP 群集。

![HDInsight 受控識別操作員角色指派](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>網路考量事項

> [!NOTE]  
> Azure AD DS 必須部署在基於 Azure 資源管理器的虛擬網路中。 Azure AD DS 不支援經典虛擬網路。 有關詳細資訊，請參閱使用[Azure 門戶啟用 Azure 活動目錄域服務](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)。

啟用 Azure AD DS 後，本地網域名稱系統 （DNS） 伺服器在活動目錄虛擬機器 （VM） 上運行。 將 Azure AD DS 虛擬網路配置為使用這些自訂 DNS 伺服器。 要查找正確的 IP 位址，請選擇 **"管理**類別中**的屬性**"，然後查看**VIRTUAL 網路 上的 IP 位址**。

![查找本地 DNS 伺服器的 IP 位址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

通過在"**設置"** 類別中選擇**DNS 伺服器**，更改 Azure AD DS 虛擬網路中 DNS 伺服器的配置以使用這些自訂 IP。 然後選擇 **"自訂"** 選項，在文字方塊中輸入第一個 IP 位址，然後選擇 **"保存**"。 使用相同的步驟添加更多 IP 位址。

![更新虛擬網路 DNS 配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

將 Azure AD DS 執行個體和 HDInsight 叢集放在相同的 Azure 虛擬網路中比較容易。 如果計畫使用不同的虛擬網路，則必須對這些虛擬網路進行對等，以便網域控制站對 HDInsight VM 可見。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。 

對等虛擬網路後，將 HDInsight 虛擬網路配置為使用自訂 DNS 伺服器，並將 Azure AD DS 專用 IP 作為 DNS 伺服器位址輸入。 當兩個虛擬網路使用相同的 DNS 伺服器時，您的自訂功能變數名稱將解析為正確的 IP，並且可以從 HDInsight 訪問。 例如，如果您的功能變數名稱是`contoso.com`，則在此步驟之後，`ping contoso.com`應解析為正確的 Azure AD DS IP。

![為對等虛擬網路配置自訂 DNS 伺服器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果您在 HDInsight 子網中使用網路安全性群組 （NSG） 規則，則應允許對入站和出站流量[所需的 IP。](../hdinsight-management-ip-addresses.md)

要測試網路設置是否正確，請將 Windows VM 加入 HDInsight 虛擬網路/子網並 ping 功能變數名稱。 （它應該解析為 IP。運行**ldp.exe**以訪問 Azure AD DS 域。 然後，將此 Windows VM 加入域，以確認用戶端和伺服器之間所需的所有 RPC 調用是否成功。 

您還可以使用**nslookup**確認對存儲帳戶或任何可能使用的外部資料庫（例如，外部 Hive 元存儲或 Ranger DB）的網路訪問。 如果 NSG 有助於保護 Azure AD DS，請確保 Azure AD DS 子網的 NSG 規則中允許所有[必需的埠](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)。 如果此 Windows VM 的域加入成功，則可以繼續執行下一步並創建 ESP 群集。

## <a name="create-an-hdinsight-cluster-with-esp"></a>使用 ESP 創建 HDInsight 群集

正確設置上述步驟後，下一步是創建啟用 ESP 的 HDInsight 群集。 創建 HDInsight 群集時，您可以在 **"安全 + 網路"** 選項卡上啟用企業安全包。如果希望使用 Azure 資源管理器範本進行部署，請使用門戶體驗一次，並在 **"審核 + 創建**"頁上下載預填充範本以供將來重用。 

您還可以在群集創建期間啟用[HDInsight ID 代理](identity-broker.md)功能。 ID 代理功能允許您使用多重要素驗證登錄到 Ambari，並獲取所需的 Kerberos 票證，而無需在 Azure AD DS 中進行密碼雜湊。

> [!NOTE]  
> ESP 叢集名稱的前六個字元在您的環境中必須是唯一的。 例如，如果在不同的虛擬網路中有多個 ESP 群集，請選擇一個命名約定，以確保群集名稱上的前六個字元是唯一的。

![Azure HDInsight 企業安全包的域驗證](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

啟用 ESP 後，將自動檢測和驗證與 Azure AD DS 相關的常見錯誤配置。 修復這些錯誤後，可以繼續執行下一步。

![Azure HDInsight 企業安全包失敗域驗證](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

使用 ESP 創建 HDInsight 群集時，必須提供以下參數：

- **群集管理員使用者**：從同步的 Azure AD DS 實例中選擇群集的管理員。 此域帳戶必須已同步並在 Azure AD DS 中可用。

- **群集訪問組**：要同步並有權訪問群集的使用者的安全性群組應在 Azure AD DS 中可用。 例如，HiveUsers 組。 如需詳細資訊，請參閱[在 Azure Active Directory 中建立群組和新增使用者](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：`ldaps://contoso.com:636`一個示例是 。

創建新群集時，可以從**使用者分配的託管標識**下拉清單選擇您創建的託管標識。

![Azure HDInsight ESP 活動目錄域服務託管標識](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>後續步驟

* 如需了解如何設定 Hive 原則及執行 Hive 查詢，請參閱[為具有 ESP 的 HDInsight 叢集設定 Apache Hive 原則](apache-domain-joined-run-hive.md)。
* 如需了解如何使用 SSH 來連線到具有 ESP 的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 使用 SSH 搭配 Linux 型 HDInsight 上的 Apache Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
