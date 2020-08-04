---
title: Azure HDInsight 中的企業安全性一般指導方針
description: 應該讓企業安全性套件部署和管理的一些最佳做法變得更容易。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 8d97886232eecc369746e33df484cbfb9d40da72
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87530260"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 中的企業安全性一般資訊和指導方針

部署安全的 HDInsight 叢集時，有一些最佳作法應該讓部署和叢集管理變得更容易。 這裡會討論一些一般資訊和指導方針。

## <a name="use-of-secure-cluster"></a>使用安全叢集

### <a name="recommended"></a>建議

* 多個使用者會同時使用叢集。
* 使用者對相同的資料具有不同層級的存取權。

### <a name="not-necessary"></a>不需要

* 您只會執行自動化工作（例如單一使用者帳戶），標準叢集就夠好了。
* 您可以使用標準叢集來進行資料匯入，並在使用者可以執行分析工作的不同安全叢集上使用相同的儲存體帳戶。

## <a name="use-of-local-account"></a>使用本機帳戶

* 如果您使用共用使用者帳戶或本機帳戶，則很難以識別使用帳戶來變更設定或服務的人員。
* 當使用者不再是組織的一部份時，使用本機帳戶會有問題。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>原則

* 根據預設，Ranger 會使用「**拒絕**」做為原則。

* 透過啟用授權的服務來進行資料存取時：
  * 會叫用 Ranger 授權外掛程式並指定要求的內容。
  * Ranger 會套用針對服務所設定的原則。 如果 Ranger 原則失敗，則會將存取檢查延後至檔案系統。 某些服務（例如 MapReduce）只會檢查正在提交要求的同一位使用者所擁有的檔案/資料夾。 Hive 之類的服務，檢查擁有權是否符合或適當的檔案系統許可權（ `rwx` ）。

* 針對 Hive，除了擁有建立/更新/刪除許可權的許可權之外，使用者還應該擁有 `rwx` 儲存體和所有子目錄上目錄的許可權。

* 原則可以套用至群組（較理想），而不是個人。

* Ranger 授權者將會針對每個要求評估該服務的所有 Ranger 原則。 此評估可能會影響接受作業或查詢所花費的時間。

### <a name="storage-access"></a>儲存體存取

* 如果儲存體類型為 WASB，則不會涉及任何 OAuth token。
* 如果 Ranger 已執行授權，則會使用受控識別來進行儲存體存取。
* 如果 Ranger 未執行任何授權，則會使用使用者的 OAuth 權杖來進行儲存體存取。

### <a name="hierarchical-name-space"></a>階層式命名空間

未啟用階層式命名空間時：

* 沒有繼承的許可權。
* 只有作用中的 filesystem 許可權是**儲存體資料 XXXX** Azure 角色，才會直接指派給使用者 Azure 入口網站。

### <a name="default-hdfs-permissions"></a>預設 HDFS 許可權

* 根據預設，使用者無法存取 **/** HDFS 上的資料夾（他們必須在儲存體 blob 擁有者角色中，才能成功存取）。
* 針對 mapreduce 和其他專案的臨時目錄，會建立並提供許可權給使用者特定目錄 `sticky _wx` 。 使用者可以在底下建立檔案和資料夾，但無法查看其他專案。

### <a name="url-auth"></a>URL 驗證

如果已啟用 url 驗證：

* 此設定會包含 url 驗證中涵蓋的首碼（例如 `adl://` ）。
* 如果此 url 的存取權，則 Ranger 會檢查使用者是否在允許清單中。
* Ranger 不會檢查任何精細的原則。

## <a name="resource-groups"></a>資源群組

針對每個叢集使用新的資源群組，以便您可以區分叢集資源。

## <a name="nsgs-firewalls-and-internal-gateway"></a>Nsg、防火牆和內部閘道

* 使用網路安全性群組（Nsg）來鎖定虛擬網路。
* 使用防火牆來處理輸出存取原則。
* 使用未開放給公用網際網路的內部閘道。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) （Azure AD）是 Microsoft 的雲端式身分識別和存取管理服務。

### <a name="policies"></a>原則

* 使用以 IP 位址為基礎的原則來停用條件式存取原則。 這需要在部署叢集的 Vnet 上啟用服務端點。 如果您使用外部服務進行 MFA （AAD 以外的專案），則以 IP 位址為基礎的原則將無法使用

* `AllowCloudPasswordValidation`同盟使用者需要原則。 由於 HDInsight 會直接使用使用者名稱/密碼來取得來自 Azure AD 的權杖，因此必須為所有同盟使用者啟用此原則。

* 如果您需要使用受信任的 Ip 來略過條件式存取，請啟用服務端點。

### <a name="groups"></a>群組

* 一律部署具有群組的叢集。
* 使用 Azure AD 來管理群組成員資格（比嘗試管理叢集中的個別服務更容易）。

### <a name="user-accounts"></a>使用者帳戶

* 針對每個案例使用唯一的使用者帳戶。 例如，使用帳戶進行匯入，使用另一個用於查詢或其他處理作業。
* 使用以群組為基礎的 Ranger 原則，而不是個別的原則。
* 規劃如何管理不應再存取叢集的使用者。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) （Azure AD DS）提供受控網域服務，例如加入網域、群組原則、輕量型目錄存取協定（LDAP），以及與 Windows Server Active Directory 完全相容的 KERBEROS/NTLM 驗證。

需要 Azure AD DS，安全叢集才能加入網域。
HDInsight 無法依賴內部部署網域控制站或自訂網域控制站，因為它引進了太多的錯誤點、認證共用、DNS 許可權等等。 如需詳細資訊，請參閱[AZURE AD DS 常見問題](../../active-directory-domain-services/faqs.md)。

### <a name="azure-ad-ds-instance"></a>Azure AD DS 實例

* 使用建立實例 `.onmicrosoft.com domain` 。 如此一來，就不會有多部 DNS 伺服器為網域提供服務。
* 建立 LDAPS 的自我簽署憑證，並將它上傳至 Azure AD DS。
* 使用對等互連虛擬網路來部署叢集（當您有許多小組部署 HDInsight ESP 叢集時，這會很有説明）。 這可確保您不需要在具有網域控制站的虛擬網路上開啟埠（Nsg）。
* 適當地設定虛擬網路的 DNS （Azure AD DS 功能變數名稱應解析，而不需要任何主機檔案專案）。
* 如果您要限制輸出流量，請確定您已閱讀[HDInsight 中的防火牆支援](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>從 Azure AD 同步到 Azure AD DS 的屬性

* Azure AD 將同步處理從內部部署連線到 Azure AD。
* Azure AD DS 從 Azure AD 同步處理。

Azure AD DS 會定期從 Azure AD 同步處理物件。 Azure 入口網站上的 [Azure AD DS] 分頁會顯示同步處理狀態。 在同步處理的每個階段中，唯一的屬性可能會發生衝突並重新命名。 請注意從 Azure AD 到 Azure AD DS 的屬性對應。

如需詳細資訊，請參閱[Azure AD UserPrincipalName 填入](../../active-directory/hybrid/plan-connect-userprincipalname.md)和[Azure AD DS 同步處理的運作方式](../../active-directory-domain-services/synchronization.md)。

### <a name="password-hash-sync"></a>密碼雜湊同步處理

* 密碼的同步與其他物件類型不同。 只有不可逆的密碼雜湊會在 Azure AD 和 Azure AD DS 中同步處理
* 內部部署至 Azure AD 必須透過 AD Connect 啟用
* Azure AD DS 同步處理的 Azure AD 為自動（延遲時間為20分鐘）。
* 只有在有變更的密碼時，才會同步處理密碼雜湊。 當您啟用密碼雜湊同步處理時，所有現有的密碼都不會在將失效儲存時自動同步。 當您變更密碼時，密碼雜湊就會同步處理。

### <a name="computer-objects-location"></a>電腦物件位置

每個叢集都與單一 OU 相關聯。 在 OU 中布建了內部使用者。 所有節點都會加入相同的 OU 中。

### <a name="active-directory-administrative-tools"></a>Active Directory 系統管理工具

如需有關如何在 Windows Server VM 上安裝 Active Directory 系統管理工具的步驟，請參閱[安裝管理工具](../../active-directory-domain-services/tutorial-create-management-vm.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="cluster-creation-fails-repeatedly"></a>叢集建立重複失敗

最常見的原因：

* DNS 設定不正確，叢集節點的網域加入失敗。
* Nsg 太過限制，因此無法加入網域。
* 受控識別沒有足夠的許可權。
* 在前六個字元（與另一個即時叢集或已刪除的叢集）上，叢集名稱不是唯一的。

## <a name="authentication-setup-and-configuration"></a>驗證安裝和設定

### <a name="user-principal-name-upn"></a>使用者主要名稱 (UPN)

* 所有服務都請使用小寫-Upn 在 ESP 叢集中不區分大小寫，但
* UPN 前置詞應符合 Azure AD DS 中的 SAMAccountName。 不需要與 [郵件] 欄位相符。

### <a name="ldap-properties-in-ambari-configuration"></a>Ambari 設定中的 LDAP 屬性

如需影響 HDInsight 叢集設定之 Ambari 屬性的完整清單，請參閱[AMBARI LDAP 驗證安裝](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)。

## <a name="next-steps"></a>後續步驟

* [在 HDInsight 中使用 Azure Active Directory Domain Services 的企業安全性套件設定](./apache-domain-joined-configure-using-azure-adds.md)

* [將 Azure Active Directory 使用者同步處理至 HDInsight](../hdinsight-sync-aad-users-to-cluster.md)叢集。
