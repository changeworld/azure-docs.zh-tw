---
title: Azure AD Domain Services 中的常見警示和解決方式 |Microsoft Docs
description: 瞭解如何解決 Azure Active Directory Domain Services 的健全狀況狀態中產生的常見警示
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: ed791ea10c072308c16baac9fa469a46b19ec648
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654506"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>已知問題： Azure Active Directory Domain Services 中的常見警示和解決方式

作為應用程式身分識別和驗證的核心部分，Azure Active Directory Domain Services （Azure AD DS）有時會發生問題。 如果您遇到問題，有一些常見的警示和相關的疑難排解步驟可協助您再次執行專案。 您也可以隨時[開啟 Azure 支援要求][azure-support]，以取得額外的疑難排解協助。

本文提供 Azure AD DS 中常見警示的疑難排解資訊。

## <a name="aadds100-missing-directory"></a>AADDS100：遺失目錄

### <a name="alert-message"></a>警示訊息

*與受控網域相關聯的 Azure AD 目錄可能已刪除。受控網域已不在支援的設定中。Microsoft 無法監視、管理、修補及同步處理您的受控網域。*

### <a name="resolution"></a>解決方法

此錯誤通常是因為 Azure 訂用帳戶移至新的 Azure AD 目錄，而且已刪除與 Azure AD DS 相關聯的舊 Azure AD 目錄所造成。

此錯誤無法復原。 若要解決警示，請[刪除現有的 AZURE AD DS 受控網域](delete-aadds.md)，然後在新的目錄中重新建立它。 如果您無法刪除 Azure AD DS 受控網域，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目錄中執行

### <a name="alert-message"></a>警示訊息

無法在 Azure AD B2C 目錄中啟用 Azure AD Domain Services。**

### <a name="resolution"></a>解決方法

Azure AD DS 會自動與 Azure AD 目錄進行同步處理。 如果為 B2C 設定了 Azure AD 目錄，Azure AD DS 就無法部署及同步處理。

若要使用 Azure AD DS，您必須使用下列步驟，在非 Azure AD B2C 目錄中重新建立受控網域：

1. 從現有的 Azure AD 目錄中[刪除 AZURE AD DS 受控網域](delete-aadds.md)。
1. 建立不是 Azure AD B2C 目錄的新 Azure AD 目錄。
1. [建立替代 AZURE AD DS 受控網域](tutorial-create-instance.md)。

Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：位址位於公用 IP 範圍中

### <a name="alert-message"></a>警示訊息

*您已啟用 Azure AD Domain Services 之虛擬網路的 IP 位址範圍是在公用 IP 範圍中。必須在具有私人 IP 位址範圍的虛擬網路中啟用 Azure AD Domain Services。此設定會影響 Microsoft 監視、管理、修補及同步處理受控網域的能力。*

### <a name="resolution"></a>解決方法

開始之前，請確定您瞭解[私人 IP v4 位址空間](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)。

在虛擬網路內部，Vm 可以向已針對子網設定的相同 IP 位址範圍中的 Azure 資源提出要求。 如果您設定子網的公用 IP 位址範圍，在虛擬網路內路由的要求可能無法到達預定的 web 資源。 此設定可能會導致 Azure AD DS 發生無法預期的錯誤。

> [!NOTE]
> 如果您擁有虛擬網路中所設定的網際網路 IP 位址範圍，便可以忽略此警示。 不過，Azure AD Domain Services 無法使用此設定來認可[SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)]，因為這可能會導致無法預期的錯誤。

若要解決此警示，請刪除現有的 Azure AD DS 受控網域，並在具有私人 IP 位址範圍的虛擬網路中重新建立它。 因為 Azure AD DS 受控網域無法使用，而且您建立的任何自訂資源（例如 Ou 或服務帳戶）遺失，此程式會造成干擾。

1. 從您的目錄中[刪除 AZURE AD DS 受控網域](delete-aadds.md)。
1. 若要更新虛擬網路 IP 位址範圍，請在 Azure 入口網站中搜尋並選取 [*虛擬網路*]。 選取未正確設定公用 IP 位址範圍之 Azure AD DS 的虛擬網路。
1. 在 [**設定**] 底下，選取 [*位址空間*]。
1. 選擇現有的位址範圍並進行編輯，或新增額外的位址範圍，以更新位址範圍。 請確定新的 IP 位址範圍是在私人 IP 範圍中。 準備就緒時，**儲存**變更。
1. 選取左側導覽中的 [**子網**]。
1. 選擇您想要編輯的子網，或建立額外的子網。
1. 更新或指定私人 IP 位址範圍，然後**儲存**您的變更。
1. [建立替代 AZURE AD DS 受控網域](tutorial-create-instance.md)。 請確定您選擇的是已更新的虛擬網路子網，其具有私人 IP 位址範圍。

Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106：找不到 Azure 訂用帳戶

### <a name="alert-message"></a>警示訊息

*與您的受控網域相關聯的 Azure 訂用帳戶已刪除。 Azure AD Domain Services 需要有效的訂用帳戶，才能繼續正常運作。*

### <a name="resolution"></a>解決方法

Azure AD DS 需要有效的訂用帳戶，且無法移至不同的訂用帳戶。 如果已刪除與 Azure AD DS 受控網域相關聯的 Azure 訂用帳戶，您必須重新建立 Azure 訂用帳戶和 Azure AD DS 受控網域。

1. [建立 Azure 訂](../cost-management-billing/manage/create-subscription.md)用帳戶。
1. 從現有的 Azure AD 目錄中[刪除 AZURE AD DS 受控網域](delete-aadds.md)。
1. [建立替代 AZURE AD DS 受控網域](tutorial-create-instance.md)。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107：Azure 訂用帳戶已停用

### <a name="alert-message"></a>警示訊息

*與您的受控網域相關聯的 Azure 訂用帳戶未啟用。 Azure AD Domain Services 需要有效的訂用帳戶，才能繼續正常運作。*

### <a name="resolution"></a>解決方法

Azure AD DS 需要有效的訂用帳戶。 如果與 Azure AD DS 受控網域相關聯的 Azure 訂用帳戶不在作用中，您必須更新它以重新啟用訂用帳戶。

1. [更新 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 更新訂用帳戶之後，Azure AD DS 通知可讓您重新啟用受控網域。

再次啟用受控網域時，Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108：訂用帳戶移動目錄

### <a name="alert-message"></a>警示訊息

*Azure AD Domain Services 使用的訂用帳戶已移至另一個目錄。Azure AD Domain Services 必須在相同的目錄中有有效的訂用帳戶，才能正常運作。*

### <a name="resolution"></a>解決方法

Azure AD DS 需要有效的訂用帳戶，且無法移至不同的訂用帳戶。 如果已移動與 Azure AD DS 受控網域相關聯的 Azure 訂用帳戶，請將訂用帳戶移回前一個目錄，或從現有的目錄中[刪除您的受控網域](delete-aadds.md)，然後[在所選的訂用帳戶中建立替代的 Azure AD DS 受控網域](tutorial-create-instance.md)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109：找不到您受控網域的資源

### <a name="alert-message"></a>警示訊息

*已刪除用於受控網域的資源。需要此資源，Azure AD Domain Services 才能正常運作。*

### <a name="resolution"></a>解決方法

Azure AD DS 會建立可正常運作的其他資源，例如公用 IP 位址、虛擬網路介面和負載平衡器。 如果刪除這些資源的任何一項，則受控網域會處於不支援的狀態，並防止管理網域。 如需這些資源的詳細資訊，請參閱[AZURE AD DS 使用的網路資源](network-considerations.md#network-resources-used-by-azure-ad-ds)。

當其中一個必要資源被刪除時，就會產生此警示。 如果資源刪除時間不到4小時前，Azure 平臺可能會自動重新建立已刪除的資源。 下列步驟概述如何檢查資源刪除的健全狀況狀態和時間戳記：

1. 在 [Azure 入口網站中，搜尋並選取 [**網域服務**]。 選擇您的 Azure AD DS 受控網域，例如*aaddscontoso.com*。
1. 在左側導覽中，選取 [**健康**情況]。
1. 在 [健康情況] 頁面中，選取識別碼為*AADDS109*的警示。
1. 警示在第一次找到時，會有時間戳記。 如果該時間戳記小於4小時前，Azure 平臺可能會自動重新建立資源，並自行解決警示。

    如果警示超過4小時，則 Azure AD DS 受控網域會處於無法復原的狀態。 [刪除 AZURE AD DS 受控網域](delete-aadds.md)，然後[建立替代的受控網域](tutorial-create-instance.md)。

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110：與受控網域相關聯的子網路已滿

### <a name="alert-message"></a>警示訊息

*所選取要部署 Azure AD Domain Services 的子網路已滿，而且也沒有空間可供建立其他必要的網域控制站。*

### <a name="resolution"></a>解決方法

Azure AD DS 的虛擬網路子網必須有足夠的 IP 位址可用於自動建立的資源。 此 IP 位址空間包括在發生維護事件時，建立取代資源的需求。 若要將可用的 IP 位址用盡的風險降到最低，請不要將其他資源（例如您自己的 Vm）部署到與 Azure AD DS 相同的虛擬網路子網中。

此錯誤無法復原。 若要解決警示，請[刪除現有的 AZURE AD DS 受控網域](delete-aadds.md)，然後重新建立。 如果您無法刪除 Azure AD DS 受控網域，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111：服務主體未獲授權

### <a name="alert-message"></a>警示訊息

*Azure AD Domain Services 用來為您的網域提供服務的服務主體未獲授權，無法管理 Azure 訂用帳戶上的資源。服務主體必須取得許可權，才能為您的受控網域進行服務。*

### <a name="resolution"></a>解決方法

部分自動產生的服務主體可用來管理和建立 Azure AD DS 受控網域的資源。 如果其中一個服務主體的存取權限已變更，網域將無法正確地管理資源。 下列步驟說明如何瞭解並將存取權限授與服務主體：

1. 閱讀有關[角色型存取控制，以及如何在 Azure 入口網站中授與應用程式存取權的](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)相關資訊。
2. 檢查具有識別碼*abba844e-bc0e-44b0-947a-dc74e5d09022*之服務主體的存取權，並授與在較早日期拒絕的存取權。

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112：受控網域中沒有足夠的 IP 位址

### <a name="alert-message"></a>警示訊息

*我們識別出此網域中的虛擬網路子網可能沒有足夠的 IP 位址。Azure AD Domain Services 需要在其啟用的子網內至少有兩個可用的 IP 位址。我們建議在子網內至少有3-5 的備用 IP 位址。如果在子網內部署其他虛擬機器，因而耗盡可用的 IP 位址數目，或子網中的可用 IP 位址數目有所限制，就可能發生這種情況。*

### <a name="resolution"></a>解決方法

Azure AD DS 的虛擬網路子網需要有足夠的 IP 位址可用於自動建立的資源。 此 IP 位址空間包括在發生維護事件時，建立取代資源的需求。 若要將可用的 IP 位址用盡的風險降到最低，請不要將其他資源（例如您自己的 Vm）部署到與 Azure AD DS 相同的虛擬網路子網中。

若要解決此警示，請刪除現有的 Azure AD DS 受控網域，然後在具有足夠 IP 位址範圍的虛擬網路中重新建立它。 因為 Azure AD DS 受控網域無法使用，而且您建立的任何自訂資源（例如 Ou 或服務帳戶）遺失，此程式會造成干擾。

1. 從您的目錄中[刪除 AZURE AD DS 受控網域](delete-aadds.md)。
1. 若要更新虛擬網路 IP 位址範圍，請在 Azure 入口網站中搜尋並選取 [*虛擬網路*]。 針對具有小型 IP 位址範圍的 Azure AD DS 選取虛擬網路。
1. 在 [**設定**] 底下，選取 [*位址空間*]。
1. 選擇現有的位址範圍並進行編輯，或新增額外的位址範圍，以更新位址範圍。 請確定新的 IP 位址範圍夠大，可用於 Azure AD DS 子網範圍內。 準備就緒時，**儲存**變更。
1. 選取左側導覽中的 [**子網**]。
1. 選擇您想要編輯的子網，或建立額外的子網。
1. 更新或指定夠大的 IP 位址範圍，然後**儲存**您的變更。
1. [建立替代 AZURE AD DS 受控網域](tutorial-create-instance.md)。 請確定您選擇的是已更新的虛擬網路子網，其 IP 位址範圍夠大。

Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113：無法復原資源

### <a name="alert-message"></a>警示訊息

*偵測到 Azure AD Domain Services 使用的資源處於非預期狀態且無法復原。*

### <a name="resolution"></a>解決方法

此錯誤無法復原。 若要解決警示，請[刪除現有的 AZURE AD DS 受控網域](delete-aadds.md)，然後重新建立。 如果您無法刪除 Azure AD DS 受控網域，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

## <a name="aadds114-subnet-invalid"></a>AADDS114：子網路無效

### <a name="alert-message"></a>警示訊息

*選取要部署 Azure AD Domain Services 的子網無效，因此無法使用。*

### <a name="resolution"></a>解決方法

此錯誤無法復原。 若要解決警示，請[刪除現有的 AZURE AD DS 受控網域](delete-aadds.md)，然後重新建立。 如果您無法刪除 Azure AD DS 受控網域，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

## <a name="aadds115-resources-are-locked"></a>AADDS115：資源遭鎖定

### <a name="alert-message"></a>警示訊息

*因為目標範圍已鎖定，而無法操作一或多個使用受控網域的網路資源。*

### <a name="resolution"></a>解決方法

資源鎖定可以套用至 Azure 資源，以避免變更或刪除。 因為 Azure AD DS 是受控服務，所以 Azure 平臺需要進行設定變更的功能。 如果資源鎖定套用在某些 Azure AD DS 元件上，Azure 平臺就無法執行其管理工作。

若要檢查 Azure AD DS 元件上的資源鎖定，並將它們移除，請完成下列步驟：

1. 針對資源群組中的每個 Azure AD DS 網路元件（例如虛擬網路、網路介面或公用 IP 位址），檢查 Azure 入口網站中的作業記錄。 這些作業記錄應該會指出作業失敗的原因，以及套用資源鎖定的位置。
1. 選取已套用鎖定的資源，然後在 [**鎖定**] 底下，選取並移除鎖定。

## <a name="aadds116-resources-are-unusable"></a>AADDS116：無法使用資源

### <a name="alert-message"></a>警示訊息

*由於原則限制，而無法操作一或多個使用受控網域的網路資源。*

### <a name="resolution"></a>解決方法

原則會套用至 Azure 資源和資源群組，以控制允許的設定動作。 因為 Azure AD DS 是受控服務，所以 Azure 平臺需要進行設定變更的功能。 如果原則套用在某些 Azure AD DS 元件上，Azure 平臺可能無法執行其管理工作。

若要檢查 Azure AD DS 元件上已套用的原則並加以更新，請完成下列步驟：

1. 針對資源群組中的每個 Azure AD DS 網路元件（例如虛擬網路、NIC 或公用 IP 位址），檢查 Azure 入口網站中的作業記錄。 這些作業記錄應該會指出作業失敗的原因，以及套用限制原則的位置。
1. 選取套用原則的資源，然後在 [**原則**] 下選取並編輯原則，讓它的限制較少。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步處理有一陣子未完成

### <a name="alert-message"></a>警示訊息

*受控網域上次與 [date] 上的 Azure AD 同步處理。使用者可能無法登入受控網域，或群組成員資格可能不會與 Azure AD 同步。*

### <a name="resolution"></a>解決方法

[檢查 AZURE AD DS 健全狀況](check-health.md)，以取得指出受控網域設定中有問題的任何警示。 網路設定的問題可能會封鎖 Azure AD 的同步處理。 如果您能夠解決指出設定問題的警示，請等候兩個小時並回頭查看同步處理是否已順利完成。

下列常見原因會導致 Azure AD DS 受控網域中的同步處理停止：

* 已封鎖所需的網路連線。 若要深入瞭解如何檢查 Azure 虛擬網路是否有問題和所需的資訊，請參閱針對[網路安全性群組](alert-nsg.md)和[Azure AD Domain Services 的網路需求](network-considerations.md)進行疑難排解。
*  部署 Azure AD DS 受控網域時，未設定或成功完成密碼同步處理。 您可以針對[僅限雲端的使用者](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)或[混合式使用者，設定從內部內部部署](tutorial-configure-password-hash-sync.md)的密碼同步處理。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：有一陣子未進行備份

### <a name="alert-message"></a>警示訊息

*受控網域前次是在 [date] 進行備份。*

### <a name="resolution"></a>解決方法

[檢查 AZURE AD DS 健全狀況](check-health.md)，以取得指出受控網域設定中有問題的警示。 網路設定的問題可能會封鎖 Azure 平臺，使其無法成功取得備份。 如果您能夠解決指出設定問題的警示，請等候兩個小時並回頭查看同步處理是否已順利完成。

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：因為停用的訂用帳戶而造成擱置

### <a name="alert-message"></a>警示訊息

*受控網域已擱置，因為與此網域相關聯的 Azure 訂用帳戶不在作用中。*

### <a name="resolution"></a>解決方法

> [!WARNING]
> 如果 Azure AD DS 受控網域已暫止一段時間，就會有風險遭到刪除。 儘快解決暫停的原因。 如需詳細資訊，請參閱[瞭解 AZURE AD DS 的已暫停狀態](suspension.md)。

Azure AD DS 需要有效的訂用帳戶。 如果與 Azure AD DS 受控網域相關聯的 Azure 訂用帳戶不在作用中，您必須更新它以重新啟用訂用帳戶。

1. [更新 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 更新訂用帳戶之後，Azure AD DS 通知可讓您重新啟用受控網域。

再次啟用受控網域時，Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：因為無效的組態而造成擱置

### <a name="alert-message"></a>警示訊息

*因為設定無效，所以已暫止受控網域。服務已有很長一段時間無法管理、修補或更新受控網域的網域控制站。*

### <a name="resolution"></a>解決方法

> [!WARNING]
> 如果 Azure AD DS 受控網域已暫止一段時間，就會有風險遭到刪除。 儘快解決暫停的原因。 如需詳細資訊，請參閱[瞭解 AZURE AD DS 的已暫停狀態](suspension.md)。

[檢查 AZURE AD DS 健全狀況](check-health.md)，以取得指出受控網域設定中有問題的警示。 如果您能夠解決指出設定問題的警示，請等候兩個小時並回頭查看同步處理是否已完成。 準備好時，請[開啟 Azure 支援要求][azure-support]以重新啟用 Azure AD DS 受控網域。

## <a name="next-steps"></a>後續步驟

如果您仍有問題，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
