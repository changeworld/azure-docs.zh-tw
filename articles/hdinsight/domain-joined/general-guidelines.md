---
title: Azure HDInsight 中的企業安全一般準則
description: 一些最佳做法，應該使企業安全包的部署和管理更容易。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463202"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 中的企業安全一般資訊和指南

部署安全 HDInsight 群集時，有一些最佳做法應使部署和群集管理更加輕鬆。 這裡討論了一些一般性資訊和準則。

## <a name="use-of-secure-cluster"></a>使用安全群集

### <a name="recommended"></a>建議

* 群集將由多個使用者同時使用。
* 使用者對相同資料具有不同級別的存取權限。

### <a name="not-necessary"></a>不需要

* 您將只運行自動作業（如單個使用者帳戶），標準群集足夠好。
* 您可以使用標準群集執行資料導入，並在使用者可以運行分析作業的不同安全群集上使用相同的存儲帳戶。

## <a name="use-of-local-account"></a>使用本地帳戶

* 如果您使用共用使用者帳戶或本地帳戶，則很難確定誰使用該帳戶來更改配置或服務。
* 當使用者不再是組織的一部分時，使用本地帳戶存在問題。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>原則

* 預設情況下，遊俠使用**拒絕**作為策略。

* 通過啟用授權的服務進行資料訪問：
  * 將調用 Ranger 授權外掛程式，並給出請求的上下文。
  * 遊俠應用為服務配置的策略。 如果 Ranger 策略失敗，訪問檢查將延遲到檔案系統。 某些服務（如 MapReduce）僅檢查提交請求的同一使用者是否擁有該檔/資料夾。 服務（如 Hive），檢查擁有權匹配或適當的檔案系統許可權 （）。`rwx`

* 對於 Hive，除了具有執行"創建/更新/刪除"許可權的許可權外，使用者還應對存儲`rwx`目錄和所有子目錄具有許可權。

* 策略可以應用於組（首選），而不是個人。

* 遊俠授權者將為每個請求評估該服務的所有遊俠策略。 此評估可能會影響接受作業或查詢所花的時間。

### <a name="storage-access"></a>存儲訪問

* 如果存儲類型為 WASB，則不涉及 OAuth 權杖。
* 如果 Ranger 已執行授權，則存儲訪問將使用託管標識進行。
* 如果 Ranger 未執行任何授權，則存儲訪問將使用使用者的 OAuth 權杖進行。

### <a name="hierarchical-name-space"></a>分層名稱空間

未啟用中的分層名稱空間時：

* 沒有繼承的許可權。
* 只有有效的檔案系統許可權是**存儲資料 XXXX** RBAC 角色，該角色將直接分配給 Azure 門戶中的使用者。

### <a name="default-hdfs-permissions"></a>預設 HDFS 許可權

* 預設情況下，使用者無法訪問 HDFS 上的**/** 資料夾（他們需要位於存儲 Blob 擁有者角色中才能成功訪問）。
* 對於 mapreduce 和其他臨時目錄，將創建特定于使用者的目錄並提供`sticky _wx`許可權。 使用者可以在下面創建檔和資料夾，但不能查看其他專案。

### <a name="url-auth"></a>URL auth

如果啟用了 URL auth：

* 配置將包含 URL auth（如`adl://`） 中涵蓋的首碼。
* 如果訪問是此 URL，則 Ranger 將檢查使用者是否位於允許清單中。
* 遊俠不會檢查任何細細微性策略。

## <a name="resource-groups"></a>資源群組

為每個群集使用新的資源組，以便區分群集資源。

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG、防火牆和內部閘道

* 使用網路安全性群組 （NSG） 鎖定虛擬網路。
* 使用防火牆處理出站訪問策略。
* 使用不對公眾互聯網開放的內部閘道。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure 活動目錄](../../active-directory/fundamentals/active-directory-whatis.md)（Azure AD） 是 Microsoft 基於雲的標識和訪問管理服務。

### <a name="policies"></a>原則

* 使用基於 IP 位址的策略禁用條件訪問策略。 這要求在部署群集的 VNET 上啟用服務終結點。 如果您為 MFA 使用外部服務（AAD 以外的服務），則基於 IP 位址的策略將不起作用

* `AllowCloudPasswordValidation`聯合使用者需要策略。 由於 HDInsight 直接使用使用者名/密碼從 Azure AD 獲取權杖，因此必須為所有聯合使用者啟用此策略。

* 如果需要使用受信任的 IP 進行條件訪問繞過，則啟用服務終結點。

### <a name="groups"></a>群組

* 始終使用組部署群集。
* 使用 Azure AD 管理組成員身份（比嘗試管理群集中的單個服務更容易）。

### <a name="user-accounts"></a>使用者帳戶

* 為每個方案使用唯一的使用者帳戶。 例如，使用帳戶導入，使用另一個帳戶進行查詢或其他處理作業。
* 使用基於組的遊俠策略而不是單個策略。
* 制定計畫，瞭解如何管理不應再訪問群集的使用者。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure 活動目錄域服務](../../active-directory-domain-services/overview.md)（Azure AD DS） 提供託管域服務，如域聯接、群組原則、羽量級目錄訪問協定 （LDAP） 和與 Windows 伺服器活動目錄完全相容的 Kerberos / NTLM 身份驗證。

安全群集需要 Azure AD DS 才能加入域。
HDInsight 不能依賴于本地網域控制站或自訂網域控制站，因為它引入了太多的故障點、憑據共用、DNS 許可權等。 有關詳細資訊，請參閱[Azure AD DS 常見問題解答](../../active-directory-domain-services/faqs.md)。

### <a name="azure-ad-ds-instance"></a>Azure AD DS 實例

* 使用 創建`.onmicrosoft.com domain`實例。 這樣，不會有多個 DNS 伺服器為域提供服務。
* 為 LDAPS 創建自簽章憑證並將其上載到 Azure AD DS。
* 使用對等虛擬網路部署群集（當您有許多團隊部署 HDInsight ESP 群集時，這將很有説明）。 這可確保無需使用網域控制站在虛擬網路上打開端口 （NSG）。
* 正確配置虛擬網路的 DNS（Azure AD DS 功能變數名稱應在沒有任何主機檔條目的情況下解析）。
* 如果要限制出站流量，請確保已通讀[HDInsight 中的防火牆支援](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>從 Azure AD 同步到 Azure AD DS 的屬性

* Azure AD 將同步從本地連接到 Azure AD。
* Azure AD DS 從 Azure AD 同步。

Azure AD DS 會定期同步來自 Azure AD 的物件。 Azure 門戶上的 Azure AD DS 邊欄選項卡顯示同步狀態。 在同步的每個階段，唯一屬性可能會發生衝突並重命名。 注意從 Azure AD 到 Azure AD DS 的屬性對應。

有關詳細資訊，請參閱[Azure AD 使用者主名稱填充](../../active-directory/hybrid/plan-connect-userprincipalname.md)，以及[Azure AD DS 同步的工作原理](../../active-directory-domain-services/synchronization.md)。

### <a name="password-hash-sync"></a>密碼雜湊同步處理

* 密碼與其他物件類型的同步方式不同。 在 Azure AD 和 Azure AD DS 中僅同步不可逆的密碼雜湊
* 必須通過 AD 連接啟用到 Azure AD 的本地
* Azure AD 到 Azure AD DS 同步是自動的（延遲時間不到 20 分鐘）。
* 密碼雜湊僅在密碼更改時同步。 啟用密碼雜湊同步時，所有現有密碼不會自動同步，因為它們是不可逆的存儲。 更改密碼時，密碼雜湊將同步。

### <a name="computer-objects-location"></a>電腦物件位置

每個群集都與單個 OU 相關聯。 內部使用者在 OU 中預配。 所有節點都加入到同一 OU 中的域。

### <a name="active-directory-administrative-tools"></a>活動目錄管理工具

有關如何在 Windows 伺服器 VM 上安裝活動目錄管理工具的步驟，請參閱[安裝管理工具](../../active-directory-domain-services/tutorial-create-management-vm.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="cluster-creation-fails-repeatedly"></a>群集創建重複失敗

最常見的原因：

* DNS 配置不正確，叢集節點的域聯接失敗。
* NSG 太嚴格，阻止域加入。
* 託管標識沒有足夠的許可權。
* 群集名稱在前六個字元（與另一個即時群集或已刪除的群集中）上並不是唯一的。

## <a name="next-steps"></a>後續步驟

* [HDInsight 中具有 Azure 活動目錄域服務的企業安全包配置](./apache-domain-joined-configure-using-azure-adds.md)

* [將 Azure 活動目錄使用者同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)。
