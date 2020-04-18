---
title: Azure AD 域服務的管理概念 |微軟文件
description: 瞭解如何管理 Azure 活動目錄網域服務託管域以及使用者帳戶和密碼的行為
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: ba281ffb30801e0ae10cab10ceb95c0a3bffde2d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640015"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure 活動目錄網域服務使用者帳戶、密碼和管理的管理概念

創建和運行 Azure 活動目錄域服務 (AD DS) 託管域時,與傳統本地 AD DS 環境相比,其行為存在一些差異。 在 Azure AD DS 中使用與自託管域相同的管理工具,但不能直接存取網域控制器 (DC)。 密碼策略和密碼哈希的行為也存在一些差異,具體取決於使用者帳戶創建的來源。

此概念文章詳細介紹了如何管理 Azure AD DS 託管域以及使用者帳戶的不同行為,具體取決於其創建方式。

## <a name="domain-management"></a>網域管理

在 Azure AD DS 中,包含所有資源(如使用者和組、認證和策略)的網域控制器 (DC) 是託管服務的一部分。 對於冗餘,將創建兩個 DC 作為 Azure AD DS 託管域的一部分。 您無法登入這些 DC 以執行管理工作。 相反,您可以創建一個已加入 Azure AD DS 託管域的管理 VM,然後安裝常規的 AD DS 管理工具。 例如,您可以使用活動目錄管理中心或 Microsoft 管理主控台 (MMC) 管理主控台 (MMC) 管理單元(如 DNS 或組策略物件)。

## <a name="user-account-creation"></a>使用者帳戶建立

可以在 Azure AD DS 中以多種方式創建使用者帳戶。 大多數使用者帳戶從 Azure AD 同步,Azure AD 還可以包括從本地 ADDS 環境同步的使用者帳戶。 您還可以直接在 Azure AD DS 中手動創建帳戶。 某些功能(如初始密碼同步或密碼策略)的行為方式和位置不同。

* 可以從 Azure AD 同步使用者帳戶。 這包括直接在 Azure AD 建立的僅雲端使用者帳戶,以及使用 Azure AD Connect 從本地 AD DS 環境同步的混合使用者帳戶。
    * Azure AD DS 中的大多數使用者帳戶都是通過 Azure AD 的同步過程創建的。
* 使用者帳戶可以在 Azure AD DS 託管域中手動創建,並且在 Azure AD 中不存在。
    * 如果需要為僅在 Azure AD DS 中執行的應用程式創建服務帳戶,則可以在託管域中手動創建它們。 由於同步是 Azure AD 的一種方式,因此在 Azure AD DS 中創建的使用者帳戶不會同步回 Azure AD。

## <a name="password-policy"></a>密碼原則

Azure AD DS 包括預設密碼策略,用於定義帳戶鎖定、最大密碼期限和密碼複雜性等設置。 帳戶鎖定策略等設置適用於 Azure AD DS 中的所有使用者,而不考慮上一節中概述的使用者創建方式。 少數設置(如最小密碼長度和密碼複雜性)僅適用於直接在 Azure AD DS 中創建的使用者。

您可以建立自己的自定義密碼策略來覆蓋 Azure AD DS 中的預設策略。 然後,這些自定義策略可以根據需要應用於特定的使用者組。

有關密碼策略的應用程式的應用程式因使用者建立來源而異的詳細資訊,請參閱[託管域上的密碼和帳戶鎖定策略][password-policy]。

## <a name="password-hashes"></a>密碼哈希

若要在受控網域上驗證使用者，Azure AD DS 需要其格式適用 NT LAN Manager (NTLM) 和 Kerberos 驗證的密碼雜湊。 除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

對於僅限雲端的使用者帳戶，使用者必須變更其密碼，才能使用 Azure AD DS。 此密碼變更程序會在 Azure AD 中產生並儲存 Kerberos 和 NTLM 驗證的密碼雜湊。 在更改密碼之前,帳戶不會從 Azure AD 同步到 Azure AD DS。

對 Azure AD 連線從本地的 AD DS 環境同步的使用者,[啟用密碼的群組][hybrid-phs]。

> [!IMPORTANT]
> Azure AD 連接僅在為 Azure AD 租戶啟用 Azure AD DS 時同步舊密碼哈希。 如果僅使用 Azure AD 連接將本地 ADDS 環境與 Azure AD 同步,則不使用舊密碼哈希。
>
> 如果舊應用程式不使用 NTLM 身份驗證或 LDAP 簡單綁定,我們建議您禁用 Azure AD DS 的 NTLM 密碼哈希同步。 有關詳細資訊,請參閱[停用弱密碼套件和 NTLM 認證認證的哈希同步][secure-domain]。

一旦正確設定之後，可用的密碼雜湊就會儲存在 Azure AD DS 受控網域中。 如果您刪除 Azure AD DS 受控網域，此時儲存的任何密碼雜湊也會一併刪除。 如果以後創建 Azure AD DS 託管域,則無法重複使用 Azure AD 中的同步認證資訊 - 必須重新設定密碼哈希同步以再次儲存密碼哈希。 先前加入網域的 VM 或使用者將無法立即進行驗證 - Azure AD 需要在新的 Azure AD DS 受控網域中產生並儲存密碼雜湊。 如需詳細資訊，請參閱 [Azure AD DS 和 Azure AD Connect 的密碼雜湊同步程序][azure-ad-password-sync]。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在 Azure AD DS 受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

## <a name="forests-and-trusts"></a>樹系和信任

*林*是活動目錄域服務 (AD DS) 用於對一個或多個*域*進行分組的邏輯構造。 然後,域為使用者或組存儲物件,並提供身份驗證服務。

在 Azure AD DS 中,林只包含一個域。 本地 AD DS 林通常包含許多域。 在大型組織中,尤其是在合併和收購之後,您最終可能會擁有多個本地林,每個林都包含多個域。

默認情況下,Azure AD DS 託管域將作為*使用者*林創建。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可以直接針對 Azure AD DS 託管域進行身份驗證,例如登錄到加入網域的 VM。 當使用者林可以同步密碼哈希,並且使用者不使用專用登錄方法(如智慧卡身份驗證)時,使用者林將起作用。

在 Azure AD DS*資源*林中,用戶透過本地 AD DS 的單向林*信任*進行身份驗證。 使用此方法,使用者物件和密碼哈希不會同步到 Azure AD DS。 使用者物件和憑據僅存在於本地 AD DS 中。 此方法允許企業在 Azure 中託管依賴於經典身份驗證(如 LDAPS、Kerberos 或 NTLM)的資源和應用程式平臺,但刪除了任何身份驗證問題或疑慮。 Azure AD DS 資源樹系目前為預覽狀態。

有關 Azure AD DS 中的林類型的詳細資訊,請參閱[什麼是資源林?][concepts-forest]和[在 Azure AD DS 中,林信任如何工作?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU

在 Azure AD DS 中,可用的性能和功能基於 SKU。 創建託管域時選擇 SKU,並且可以在部署託管域後業務需求發生變化時切換 SKU。 下表概述了可用的 SKU 及其之間的差異:

| SKU 名稱   | 最大物件計數 | 備份頻率 | 出站林信任的最大數量 |
|------------|----------------------|------------------|----|
| 標準   | 無限制            | 每7天     | 0  |
| Enterprise | 無限制            | 每3天     | 5  |
| Premium    | 無限制            | 每日            | 10 |

在這些 Azure AD DS SKU 之前,使用了基於 Azure AD DS 託管域中物件數(用戶和電腦帳戶)的計費模型。 不再根據託管域中的物件數進行可變定價。

有關詳細資訊,請參閱 Azure [AD DS 定價頁][pricing]。

### <a name="managed-domain-performance"></a>託管域效能

域性能因應用程序實現身份驗證的方式而異。 其他計算資源可能有助於縮短查詢回應時間並減少同步操作所花費的時間。 隨著 SKU 級別的增加,託管域可用的計算資源也會增加。 監視應用程式的性能並規劃所需的資源。

如果業務或應用程式需要更改,並且 Azure AD DS 託管域需要額外的計算能力,則可以切換到其他 SKU。

### <a name="backup-frequency"></a>備份頻率

備份頻率確定獲取託管域快照的頻率。 備份是由 Azure 平臺管理的自動化過程。 如果託管域出現問題,Azure 支援可以説明您從備份中還原。 由於同步僅*從*Azure AD 單向發生,因此 Azure AD DS 託管域中的任何問題都不會影響 Azure AD 或本地 AD DS 環境和功能。

隨著 SKU 級別的增加,這些備份快照的頻率也會增加。 查看您的業務要求和恢復點目標 (RPO),以確定託管域所需的備份頻率。 如果業務或應用程式要求發生變化,並且需要更頻繁的備份,則可以切換到其他 SKU。

### <a name="outbound-forests"></a>出站林

上一節詳細介紹了從 Azure AD DS 託管域到本地 AD DS 環境(當前處於預覽狀態)的單向出站林信任。 SKU 確定可以為 Azure AD DS 託管域創建的最大林信任數。 查看業務和應用程式要求以確定實際需要的信任數,並選擇相應的 Azure AD DS SKU。 同樣,如果您的業務需求發生變化,並且需要創建其他林信任,則可以切換到其他 SKU。

## <a name="next-steps"></a>後續步驟

要開始,[請建立 Azure AD DS 託管域][create-instance]。

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
