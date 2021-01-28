---
title: Azure HDInsight 中的企業安全性一般指導方針
description: 您可以更輕鬆地企業安全性套件部署和管理的一些最佳作法。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933560"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 中的企業安全性一般資訊與指導方針

部署安全的 HDInsight 叢集時，有一些最佳作法可以讓部署和叢集管理更容易。 這裡會討論一些一般資訊與指導方針。

## <a name="use-of-secure-cluster"></a>使用安全叢集

### <a name="recommended"></a>建議

* 多個使用者會同時使用叢集。
* 使用者對相同資料具有不同層級的存取權。

### <a name="not-necessary"></a>不需要

* 您將只執行自動化的工作 (例如單一使用者帳戶) ，標準叢集的功能就夠好了。
* 您可以使用標準叢集進行資料匯入，並在使用者可以執行分析作業的不同安全叢集上使用相同的儲存體帳戶。

## <a name="use-of-local-account"></a>使用本機帳戶

* 如果您使用共用的使用者帳戶或本機帳戶，就很難識別誰使用帳戶來變更 config 或 service。
* 當使用者不再是組織的一部分時，使用本機帳戶會有問題。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>原則

* 根據預設，Ranger 會使用 [ **拒絕** ] 作為原則。

* 透過啟用授權的服務來進行資料存取時：
  * Ranger 授權外掛程式會被叫用，並指定要求的內容。
  * Ranger 會套用為服務設定的原則。 如果 Ranger 原則失敗，存取檢查會延後到檔案系統。 某些服務（例如 MapReduce）只會檢查正在提交要求的相同使用者所擁有的檔案/資料夾。 Hive 等服務、檢查擁有權是否相符或適當的 filesystem 許可權 (`rwx`) 。

* 針對 Hive，除了擁有建立/更新/刪除許可權的許可權之外，使用者也應該擁有 `rwx` 儲存體和所有子目錄上目錄的許可權。

* 原則可以套用至 (慣用) 的群組，而不是個人。

* Ranger 授權者將針對每個要求評估該服務的所有 Ranger 原則。 此評估可能會影響接受作業或查詢所需的時間。

### <a name="storage-access"></a>儲存體存取

* 如果儲存體類型是 WASB，則不會包含任何 OAuth 權杖。
* 如果 Ranger 已執行授權，則會使用受控識別來存取儲存體。
* 如果 Ranger 未執行任何授權，則會使用使用者的 OAuth 權杖來存取儲存體。

### <a name="hierarchical-name-space"></a>階層式命名空間

未啟用階層式命名空間時：

* 沒有繼承的許可權。
* 只有適用于 **儲存體資料 XXXX** Azure 角色的 filesystem 許可權，才能直接在 Azure 入口網站中指派給使用者。

### <a name="default-hdfs-permissions"></a>預設 HDFS 許可權

* 根據預設，使用者不能存取 **/** HDFS 上的資料夾 (它們必須位於儲存體 blob 擁有者角色中，才能成功存取) 。
* 針對 mapreduce 和其他的臨時目錄，會建立使用者特定目錄並提供 `sticky _wx` 許可權。 使用者可以在底下建立檔案和資料夾，但無法查看其他專案。

### <a name="url-auth"></a>URL 驗證

如果已啟用 url 驗證：

* 設定將包含 url 驗證 (中涵蓋的前置詞，例如 `adl://`) 。
* 如果存取此 url，則 Ranger 會檢查使用者是否位於允許清單中。
* Ranger 將不會檢查任何更細緻的原則。

## <a name="resource-groups"></a>資源群組

針對每個叢集使用新的資源群組，讓您可以區分叢集資源。

## <a name="nsgs-firewalls-and-internal-gateway"></a>Nsg、防火牆和內部閘道

* 使用 (Nsg) 的網路安全性群組來鎖定虛擬網路。
* 使用防火牆來處理輸出存取原則。
* 使用未開放至公用網際網路的內部閘道。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 是 Microsoft 的雲端式身分識別和存取管理服務。

### <a name="policies"></a>原則

* 使用以 IP 位址為基礎的原則來停用條件式存取原則。 這需要在叢集部署所在的 Vnet 上啟用服務端點。 如果您將外部服務用於 MFA (AAD) 以外的其他服務，則以 IP 位址為基礎的原則將無法運作

* `AllowCloudPasswordValidation` 同盟使用者需要原則。 因為 HDInsight 會直接使用使用者名稱/密碼來取得 Azure AD 的權杖，所以必須為所有同盟使用者啟用此原則。

* 如果您需要使用信任的 Ip 來略過條件式存取，請啟用服務端點。

### <a name="groups"></a>群組

* 一律使用群組部署叢集。
* 使用 Azure AD 來管理群組成員資格 (比嘗試管理叢集中的個別服務) 更容易。

### <a name="user-accounts"></a>使用者帳戶

* 針對每個案例使用唯一的使用者帳戶。 例如，使用匯入的帳戶，將另一個用於查詢或其他處理作業。
* 使用以群組為基礎的 Ranger 原則，而不是個別原則。
* 規劃如何管理應該不再有叢集存取權的使用者。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) 提供受控網域服務，例如網域加入、群組原則、輕量型目錄存取協定 (LDAP) ，以及與 Windows Server Active Directory 完全相容的 KERBEROS/NTLM 驗證。

需要 Azure AD DS，安全叢集才能加入網域。
HDInsight 無法相依于內部部署網域控制站或自訂網域控制站，因為它引進了太多的錯誤點、認證共用、DNS 許可權等等。 如需詳細資訊，請參閱 [AZURE AD DS 常見問題](../../active-directory-domain-services/faqs.md)。

### <a name="azure-ad-ds-instance"></a>Azure AD DS 實例

* 使用建立實例 `.onmicrosoft.com domain` 。 如此一來，就不會有多部 DNS 伺服器提供網域服務。
* 為 LDAPS 建立自我簽署的憑證，並將它上傳至 Azure AD DS。
* 使用對等互連虛擬網路部署叢集 (當您有多個部署 HDInsight ESP 叢集的小組時，這會很有説明) 。 這可確保您不需要在具有網域控制站的虛擬網路上開啟埠 (Nsg) 。
* 適當地設定虛擬網路的 DNS (必須在不) 任何主機檔案專案的情況下解析 Azure AD DS 功能變數名稱。
* 如果您要限制輸出流量，請確定您已閱讀 [HDInsight 中的防火牆支援](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>從 Azure AD 同步至 Azure AD DS 的屬性

* Azure AD 將同步從內部部署連線到 Azure AD。
* 從 Azure AD Azure AD DS 同步。

Azure AD DS 會定期從 Azure AD 同步處理物件。 Azure 入口網站上的 Azure AD DS 分頁會顯示同步處理狀態。 在同步處理的每個階段，唯一的屬性可能會發生衝突，並重新命名。 請注意從 Azure AD 到 Azure AD DS 的屬性對應。

如需詳細資訊，請參閱 [Azure AD UserPrincipalName 人口](../../active-directory/hybrid/plan-connect-userprincipalname.md)，以及 [Azure AD DS 同步處理的運作方式](../../active-directory-domain-services/synchronization.md)。

### <a name="password-hash-sync"></a>密碼雜湊同步處理

* 密碼與其他物件類型的同步處理方式不同。 只有不可還原的密碼雜湊會在 Azure AD 和 Azure AD DS 中同步處理
* 必須透過 AD Connect 啟用內部部署至 Azure AD
* Azure AD DS 同步的 Azure AD 會自動 (延遲低於20分鐘) 。
* 只有在有變更的密碼時，才會同步處理密碼雜湊。 當您啟用密碼雜湊同步處理時，所有現有的密碼都不會在將失效儲存時自動進行同步處理。 當您變更密碼時，密碼雜湊會同步處理。

### <a name="computer-objects-location"></a>電腦物件位置

每個叢集都會與單一 OU 相關聯。 系統會在 OU 中布建內部使用者。 所有節點都已加入相同的 OU 中。

### <a name="active-directory-administrative-tools"></a>Active Directory 系統管理工具

如需有關如何在 Windows Server VM 上安裝 Active Directory 系統管理工具的步驟，請參閱 [安裝管理工具](../../active-directory-domain-services/tutorial-create-management-vm.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="cluster-creation-fails-repeatedly"></a>叢集建立重複失敗

最常見的原因：

* DNS 設定不正確，叢集節點的網域加入失敗。
* Nsg 過於嚴格，因此無法加入網域。
* 受控識別沒有足夠的許可權。
* 叢集名稱在前六個字元上並不是唯一的， (與另一個即時叢集或已刪除的叢集) 。

## <a name="authentication-setup-and-configuration"></a>驗證設定和設定

### <a name="user-principal-name-upn"></a>使用者主要名稱 (UPN)

* 請針對所有服務使用小寫，ESP 叢集中的 Upn 不區分大小寫，但
* UPN 首碼應該符合 Azure AD DS 中的 SAMAccountName。 不需要與 mail 欄位相符。

### <a name="ldap-properties-in-ambari-configuration"></a>Ambari configuration 中的 LDAP 屬性

如需影響您的 HDInsight 叢集設定之 Ambari 屬性的完整清單，請參閱 [AMBARI LDAP 驗證設定](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)。

## <a name="next-steps"></a>後續步驟

* [使用 HDInsight 中的 Azure Active Directory Domain Services 企業安全性套件設定](./apache-domain-joined-configure-using-azure-adds.md)

* [將 Azure Active Directory 的使用者同步處理至 HDInsight](../hdinsight-sync-aad-users-to-cluster.md)叢集。
