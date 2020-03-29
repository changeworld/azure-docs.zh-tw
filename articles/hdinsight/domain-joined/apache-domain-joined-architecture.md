---
title: 使用企業安全性套件的 Azure HDInsight 架構
description: 瞭解如何使用企業安全包規劃 Azure HDInsight 安全性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365761"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>在 HDInsight 中使用企業安全性套件

標準的 Azure HDInsight 叢集是一個單一使用者叢集。 它適合大部分以小型應用程式團隊建置大型資料工作負載的公司。 每個使用者都可依需求建立專用的不同叢集，並且在不再需要時加以終結。

許多企業都朝向一種模型發展，亦即由 IT 小組管理叢集，而由多個應用程式小組共用叢集。 這些較大的企業需要在 Azure HDInsight 中針對每個叢集的多使用者存取。

在管理方面，HDInsight 依賴廣受使用的識別提供者 – Active Directory。 藉由 HDInsight 與 [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md) 的整合，您可以使用網域認證來存取叢集。

HDInsight 中的虛擬機器 (VM) 會加入您提供的網域。 因此，經過驗證的使用者都能夠順暢地使用在 HDInsight 上執行的所有服務 (Apache Ambari、Apache Hive 伺服器、Apache Ranger、Apache Spark Thrift 伺服器等等)。 接著，系統管理員可使用 Apache Ranger 來建立強式授權原則，為叢集中的資源提供角色型存取控制。

## <a name="integrate-hdinsight-with-active-directory"></a>整合 HDInsight 與 Active Directory

開放原始碼 Apache Hadoop 依賴 Kerberos 通訊協定來提供驗證和安全性。 因此，有企業安全性套件 (ESP) 的 HDInsight 叢集節點都會加入由 Azure AD DS 管理的網域。 針對叢集上的 Hadoop 元件，會設定 Kerberos 安全性。

系統會自動建立下列事項：

- 每個 Hadoop 元件的服務主體
- 已加入網域之每部機器的機器主體
- 每個叢集的「組織單位」(OU)，用以儲存這些服務和機器原則

總括而言，您必須設定一個具備下列條件的環境：

- Active Directory 網域 (由 Azure AD DS 管理)。 **功能變數名稱必須為 39 個字元或更少才能使用 Azure HDInsight。**
- 在 Azure AD DS 中啟用安全 LDAP (LDAPS)。
- Azure AD DS 虛擬網路和 HDInsight 虛擬網路兩者之間有適當的網路連線能力 (如果您選擇將兩者的虛擬網路分開)。 HDInsight 虛擬網路內的 VM 應可透過虛擬網路對等互連來看到 Azure AD DS。 如果 HDInsight 和 Azure AD DS 部署在相同的虛擬網路中，則會自動提供連線，而無須採取進一步的動作。

## <a name="set-up-different-domain-controllers"></a>設定不同的網域控制站

HDInsight 目前僅支援以 Azure AD DS 作為主要網域控制站，讓叢集使用 Kerberos 通訊。 但您仍然可以使用其他複雜的 Active Directory 設定，只要這類設定能夠讓 Azure AD DS 進行 HDInsight 存取即可。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) 提供與 Windows Server Active Directory 完全相容的受控網域。 Microsoft 會負責管理、修補及監視高可用性 (HA) 設定中的網域。 您可以放心地部署叢集，不必擔心網域控制站的維護問題。

使用者、群組和密碼都會從 Azure AD 同步。 從您的 Azure AD 執行個體單向同步到 Azure AD DS 可讓使用者利用相同的公司認證登入叢集。

如需詳細資訊，請參閱[使用 Azure AD DS 設定有 ESP 的 HDInsight 叢集](./apache-domain-joined-configure-using-azure-adds.md)。

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>內部部署 Active Directory 或在 IaaS VM 上的 Active Directory

如果您有內部部署的 Active Directory 執行個體或網域上有更複雜的 Active Directory 設定，則可以使用 Azure AD Connect 來將那些身分識別同步到 Azure AD。 之後，您可以在該 Active Directory 租用戶上啟用 Azure AD DS。

因為 Kerberos 依賴密碼雜湊，您必須[對 Azure AD DS 啟用密碼雜湊同步](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。

如果您正在與活動目錄聯合服務 （AD FS） 聯合使用，則必須啟用密碼雜湊同步。（有關建議的設置，請參閱[此視頻](https://youtu.be/qQruArbu2Ew)。密碼雜湊同步有助於在 AD FS 基礎結構發生故障時進行災害復原，它還有助於提供洩漏的憑據保護。 如需詳細資訊，請參閱[透過 Azure AD Connect 同步啟用密碼雜湊同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)。

針對具有 ESP 的 HDInsight 叢集，不支援單獨使用內部部署 Active Directory 或 IaaS VM 上的 Active Directory 而不搭配使用 Azure AD 和 Azure AD DS 的設定。

如果使用識別身分同盟，並且密碼雜湊正確同步，但您出現身份驗證失敗，請檢查是否為 PowerShell 服務主體啟用了雲密碼身份驗證。 如果未啟用，您就必須為 Azure AD 租用戶設定[主領域探索 (HRD) 原則](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)。 檢查並設定 HRD 原則：

1. 安裝預覽[Azure AD 電源外殼模組](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)。

   ```powershell
   Install-Module AzureAD
   ```

2. 使用全域管理員（租戶管理員）憑據進行連接。

   ```powershell
   Connect-AzureAD
   ```

3. 檢查是否已經建立 Microsoft Azure Powershell 服務主體。

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. 如果不存在，則創建服務主體。

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. 建立原則並將其附加至此服務主體。

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>後續步驟

- [設定具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md)
- [為具有 ESP 的 HDInsight 叢集設定 Apache Hive 原則](apache-domain-joined-run-hive.md)
- [管理具有 ESP 的 HDInsight 叢集](apache-domain-joined-manage.md)
