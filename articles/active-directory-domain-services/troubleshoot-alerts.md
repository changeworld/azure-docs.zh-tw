---
title: Azure AD 域服務中的常見警報和解決方法 |微軟文件
description: 瞭解如何解決作為 Azure 活動目錄域服務執行狀況的一部分生成的常見警報
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
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654506"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>已知問題:Azure 活動目錄網域服務中的常見警報和解決方法

作為應用程式識別和身份驗證的核心部分,Azure 活動目錄域服務 (Azure AD DS) 有時存在問題。 如果遇到問題,有一些常見的警報和相關故障排除步驟,以説明您重新運行操作。 您可以隨時[打開 Azure 支援請求][azure-support],以獲取其他故障排除説明。

本文提供有關 Azure AD DS 中常見警報的故障排除資訊。

## <a name="aadds100-missing-directory"></a>AADDS100：遺失目錄

### <a name="alert-message"></a>警示訊息

*與託管域關聯的 Azure AD 目錄可能已被刪除。託管域不再處於受支援的配置中。Microsoft 無法監視、管理、修補和同步託管域。*

### <a name="resolution"></a>解決方案

當 Azure 訂閱移動到新的 Azure AD 目錄並刪除與 Azure AD DS 關聯的舊 Azure AD 目錄時,通常會出現此錯誤。

此錯誤無法復原。 要解決警報,請[刪除現有的 Azure AD DS 託管域](delete-aadds.md),並在新目錄中重新創建它。 如果在刪除 Azure AD DS 託管域時遇到問題,[請打開 Azure 支援請求][azure-support]以取得其他故障排除説明。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目錄中執行

### <a name="alert-message"></a>警示訊息

無法在 Azure AD B2C 目錄中啟用 Azure AD Domain Services。**

### <a name="resolution"></a>解決方案

Azure AD DS 會自動與 Azure AD 目錄同步。 如果 Azure AD 目錄配置為 B2C,則無法部署和同步 Azure AD DS。

要使用 Azure AD DS,必須使用以下步驟在非 Azure AD B2C 目錄中重新建立託管域:

1. 從現有 Azure AD 目錄[中移除 Azure AD DS 託管域](delete-aadds.md)。
1. 創建新的 Azure AD 目錄,該目錄不是 Azure AD B2C 目錄。
1. [建立取代 Azure AD DS 託管網域](tutorial-create-instance.md)。

Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：位址位於公用 IP 範圍中

### <a name="alert-message"></a>警示訊息

*啟用 Azure AD 網域服務的虛擬網路的 IP 位址範圍在公共 IP 範圍內。必須在具有專用 IP 位址範圍的虛擬網路中啟用 Azure AD 網域服務。此配置會影響 Microsoft 監視、管理、修補和同步託管域的能力。*

### <a name="resolution"></a>解決方案

開始之前,請確保您瞭解專用 IP [v4 位址空間](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)。

在虛擬網路中,VM可以向 Azure 資源發出與為子網配置的 IP 位址範圍相同的請求。 如果為子網配置公共 IP 位址範圍,則虛擬網路中路由的請求可能無法到達預期的 Web 資源。 此配置可能導致 Azure AD DS 的不可預知錯誤。

> [!NOTE]
> 如果您擁有虛擬網路中所設定的網際網路 IP 位址範圍，便可以忽略此警示。 但是,Azure AD 功能變數服務無法在此配置下提交到[SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)*,因為它可能導致不可預知的錯誤。

要解決此警報,請刪除現有的 Azure AD DS 託管域,並在具有專用 IP 位址範圍的虛擬網路中重新創建該域。 此過程具有破壞性,因為 Azure AD DS 託管域不可用,並且您創建的任何自定義資源(如 O 或服務帳戶)都丟失。

1. 從目錄中[移除 Azure AD DS 託管網域](delete-aadds.md)。
1. 要更新虛擬網路 IP 位址範圍,請在 Azure 入口中搜尋並選擇*虛擬網路*。 為 Azure AD DS 選擇不正確的公共 IP 位址範圍集的虛擬網路。
1. 在 **「設定」** 下,選擇 *「地址空間*」 。。
1. 通過選擇現有位址範圍並編輯位址範圍或添加其他位址範圍來更新位址範圍。 確保新的 IP 位址範圍在專用 IP 範圍內。 準備就緒後,**保存**變更。
1. 在左邊導覽中選擇**子網**。
1. 選擇要編輯的子網,或創建其他子網。
1. 更新或指定專用 IP 位址範圍,然後**保存**更改。
1. [建立取代 Azure AD DS 託管網域](tutorial-create-instance.md)。 請確保選擇具有專用 IP 位址範圍的更新虛擬網路子網路。

Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106：找不到 Azure 訂用帳戶

### <a name="alert-message"></a>警示訊息

*與託管域關聯的 Azure 訂閱已被刪除。 Azure AD 網域服務需要活動訂閱才能繼續正常運行。*

### <a name="resolution"></a>解決方案

Azure AD DS 需要活動訂閱,不能移動到其他訂閱。 如果刪除與 Azure AD DS 託管域關聯的 Azure 訂閱,則必須重新創建 Azure 訂閱和 Azure AD DS 託管域。

1. [建立 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)。
1. 從現有 Azure AD 目錄[中移除 Azure AD DS 託管域](delete-aadds.md)。
1. [建立取代 Azure AD DS 託管網域](tutorial-create-instance.md)。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107：Azure 訂用帳戶已停用

### <a name="alert-message"></a>警示訊息

*與託管域關聯的 Azure 訂閱不處於活動狀態。 Azure AD 網域服務需要活動訂閱才能繼續正常運行。*

### <a name="resolution"></a>解決方案

Azure AD DS 需要活動訂閱。 如果 Azure AD DS 託管域關聯的 Azure 訂閱未處於活動狀態,則必須續訂該訂閱以重新啟動訂閱。

1. [更新 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 續訂訂閱後,Azure AD DS 通知允許您重新啟用託管域。

再次啟用託管域後,Azure AD DS 託管域的運行狀況會在兩小時內自動更新並刪除警報。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108：訂用帳戶移動目錄

### <a name="alert-message"></a>警示訊息

*Azure AD 網域服務使用的訂閱已移動到另一個目錄。Azure AD 網域服務需要在同一目錄中具有活動訂閱才能正常運行。*

### <a name="resolution"></a>解決方案

Azure AD DS 需要活動訂閱,不能移動到其他訂閱。 如果移動與 Azure AD DS 託管域關聯的 Azure 訂閱,請將訂閱移回以前的目錄,或從現有目錄[中移除託管域](delete-aadds.md),並在[選取訂閱中建立替換的 Azure AD DS 託管域](tutorial-create-instance.md)。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109：找不到您受控網域的資源

### <a name="alert-message"></a>警示訊息

*用於託管域的資源已被刪除。Azure AD 網域服務需要此資源才能正常運行。*

### <a name="resolution"></a>解決方案

Azure AD DS 會建立其他資源以正常運行,例如公共 IP 位址、虛擬網路介面和負載均衡器。 如果刪除這些資源中的任何一個,則託管域處於不受支援的狀態,並阻止管理域。 有關這些資源的詳細資訊,請參閱 Azure [AD DS 使用的網路資源](network-considerations.md#network-resources-used-by-azure-ad-ds)。

刪除這些必需資源之一時,將生成此警報。 如果資源在不到 4 小時前被刪除,Azure 平臺可能會自動重新創建已刪除的資源。 以下步驟概述如何檢查執行狀況和時間戳以進行資源刪除:

1. 在 Azure 門戶中,搜尋並選擇**網域服務**。 選擇 Azure AD DS 託管域,如*aaddscontoso.com*。
1. 在左側導航中,選擇 **「運行狀況**」。
1. 在運行狀況頁中,選擇ID *AADDS109*的警報。
1. 警報具有首次發現警報的時間戳。 如果時間戳小於 4 小時,Azure 平臺可能能夠自動重新創建資源並自行解決警報。

    如果警報超過 4 小時,則 Azure AD DS 託管域處於不可恢復狀態。 [刪除 Azure AD DS 託管網域](delete-aadds.md),然後[建立取代託管域](tutorial-create-instance.md)。

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110：與受控網域相關聯的子網路已滿

### <a name="alert-message"></a>警示訊息

*所選取要部署 Azure AD Domain Services 的子網路已滿，而且也沒有空間可供建立其他必要的網域控制站。*

### <a name="resolution"></a>解決方案

Azure AD DS 的虛擬網路子網需要自動建立的資源的足夠 IP 位址。 此 IP 位址空間包括在發生維護事件時需要創建替換資源。 為了將可用 IP 位址耗盡的風險降至最低,請不要將其他資源(如您自己的 VM)部署到與 Azure AD DS 相同的虛擬網路子網中。

此錯誤無法復原。 要解決警報,[請刪除現有的 Azure AD DS 託管域](delete-aadds.md)並重新建立它。 如果在刪除 Azure AD DS 託管域時遇到問題,[請打開 Azure 支援請求][azure-support]以取得其他故障排除説明。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: 服務主體未經授權

### <a name="alert-message"></a>警示訊息

*Azure AD 網域服務用於為網域提供服務的服務主體無權管理 Azure 訂閱上的資源。服務主體需要獲得服務託管域的許可權。*

### <a name="resolution"></a>解決方案

某些自動生成的服務主體用於管理和創建 Azure AD DS 託管域的資源。 如果更改了這些服務主體之一的訪問許可權,則域無法正確管理資源。 以下步驟演示如何理解服務主體,然後授予服務主體的訪問許可權:

1. 閱讀有關[基於角色的存取控制以及如何授予 Azure 入口中應用程式存取權限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。
2. 查看具有 ID *abba844e-bc0e-44b0-947a-dc74e5d09022*的服務主體具有的訪問,並授予較早日期被拒絕的訪問。

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112：受控網域中沒有足夠的 IP 位址

### <a name="alert-message"></a>警示訊息

*我們已確定此域中虛擬網路的子網可能沒有足夠的 IP 位址。Azure AD 網域服務在在其啟用的子網中至少需要兩個可用的 IP 位址。我們建議在子網中至少擁有 3-5 個備用 IP 位址。如果其他虛擬機部署在子網中,從而耗盡可用 IP 位址的數量,或者子網中的可用 IP 位址數量受到限制,則可能發生這種情況。*

### <a name="resolution"></a>解決方案

Azure AD DS 的虛擬網路子網需要足夠的 IP 位址來用於自動建立的資源。 此 IP 位址空間包括在發生維護事件時需要創建替換資源。 為了將可用 IP 位址耗盡的風險降至最低,請不要將其他資源(如您自己的 VM)部署到與 Azure AD DS 相同的虛擬網路子網中。

要解決此警報,請刪除現有的 Azure AD DS 託管域,並在具有足夠大 IP 位址範圍的虛擬網路中重新創建它。 此過程具有破壞性,因為 Azure AD DS 託管域不可用,並且您創建的任何自定義資源(如 O 或服務帳戶)都丟失。

1. 從目錄中[移除 Azure AD DS 託管網域](delete-aadds.md)。
1. 要更新虛擬網路 IP 位址範圍,請在 Azure 入口中搜尋並選擇*虛擬網路*。 為具有小 IP 位址範圍的 Azure AD DS 選擇虛擬網路。
1. 在 **「設定」** 下,選擇 *「地址空間*」 。。
1. 通過選擇現有位址範圍並編輯位址範圍或添加其他位址範圍來更新位址範圍。 確保新的 IP 位址範圍足夠大,適合 Azure AD DS 子網範圍。 準備就緒後,**保存**變更。
1. 在左邊導覽中選擇**子網**。
1. 選擇要編輯的子網,或創建其他子網。
1. 更新或指定足夠大的 IP 位址範圍,然後**保存**更改。
1. [建立取代 Azure AD DS 託管網域](tutorial-create-instance.md)。 請確保選擇具有足夠大 IP 位址範圍的更新虛擬網路子網。

Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113：無法復原資源

### <a name="alert-message"></a>警示訊息

*偵測到 Azure AD Domain Services 使用的資源處於非預期狀態且無法復原。*

### <a name="resolution"></a>解決方案

此錯誤無法復原。 要解決警報,[請刪除現有的 Azure AD DS 託管域](delete-aadds.md)並重新建立它。 如果在刪除 Azure AD DS 託管域時遇到問題,[請打開 Azure 支援請求][azure-support]以取得其他故障排除説明。

## <a name="aadds114-subnet-invalid"></a>AADDS114：子網路無效

### <a name="alert-message"></a>警示訊息

*為部署 Azure AD 網域服務而選擇的子網無效,無法使用。*

### <a name="resolution"></a>解決方案

此錯誤無法復原。 要解決警報,[請刪除現有的 Azure AD DS 託管域](delete-aadds.md)並重新建立它。 如果在刪除 Azure AD DS 託管域時遇到問題,[請打開 Azure 支援請求][azure-support]以取得其他故障排除説明。

## <a name="aadds115-resources-are-locked"></a>AADDS115：資源遭鎖定

### <a name="alert-message"></a>警示訊息

*因為目標範圍已鎖定，而無法操作一或多個使用受控網域的網路資源。*

### <a name="resolution"></a>解決方案

資源鎖可以應用於 Azure 資源,以防止更改或刪除。 由於 Azure AD DS 是託管服務,Azure 平臺需要進行配置更改的能力。 如果在某些 Azure AD DS 元件上應用了資源鎖,則 Azure 平台無法執行其管理任務。

要檢查 Azure AD DS 元件上的資源鎖並刪除它們,請完成以下步驟:

1. 對於資源組中的每個 Azure AD DS 網路元件(如虛擬網路、網路介面或公共 IP 位址),請檢查 Azure 門戶中的操作日誌。 這些操作日誌應指示操作失敗的原因以及應用資源鎖的位置。
1. 選擇應用鎖的資源,然後在 **「鎖」** 下選擇並刪除鎖。

## <a name="aadds116-resources-are-unusable"></a>AADDS116：無法使用資源

### <a name="alert-message"></a>警示訊息

*由於原則限制，而無法操作一或多個使用受控網域的網路資源。*

### <a name="resolution"></a>解決方案

策略應用於 Azure 資源和資源組,這些資源和資源組控制允許的配置操作。 由於 Azure AD DS 是託管服務,Azure 平臺需要進行配置更改的能力。 如果在某些 Azure AD DS 元件上應用了策略,則 Azure 平臺可能無法執行其管理任務。

要檢查 Azure AD DS 元件上應用的策略並更新它們,請完成以下步驟:

1. 對於資源組中的每個 Azure AD DS 網路元件(如虛擬網路、NIC 或公共 IP 位址),請檢查 Azure 門戶中的操作日誌。 這些操作日誌應指示操作失敗的原因以及應用限制性策略的位置。
1. 選擇應用策略的資源,然後在 **「策略」** 下選擇和編輯策略,使其限制更少。

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步處理有一陣子未完成

### <a name="alert-message"></a>警示訊息

*託管域上次與 Azure AD 同步是在 [日期]。使用者可能無法在託管域上登錄,或者組成員身份可能與 Azure AD 不同步。*

### <a name="resolution"></a>解決方案

[檢查 Azure AD DS 運行狀況](check-health.md)中是否有任何警報指示託管域配置中的問題。 網路配置問題可能會阻止 Azure AD 的同步。 如果能夠解決指示配置問題的警報,請等待兩個小時並返回以查看同步是否成功完成。

以下常見原因會導致同步在 Azure AD DS 託管域中停止:

* 所需的網路連接被阻止。 要瞭解有關如何檢查 Azure 虛擬網路的問題以及所需內容的更多資訊,請參閱[對網路安全組](alert-nsg.md)和[Azure AD 域服務的網路要求](network-considerations.md)進行故障排除。
*  部署 Azure AD DS 託管域時,未設置或成功完成密碼同步。 您可以為[僅雲端使用者](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)或來自[onprem 的混合使用者](tutorial-configure-password-hash-sync.md)設定密碼同步。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：有一陣子未進行備份

### <a name="alert-message"></a>警示訊息

*受控網域前次是在 [date] 進行備份。*

### <a name="resolution"></a>解決方案

[檢查 Azure AD DS 運行狀況](check-health.md)中存在指示託管域配置中出現問題的警報。 網路配置問題可能會阻止 Azure 平臺成功執行備份。 如果能夠解決指示配置問題的警報,請等待兩個小時並返回以查看同步是否成功完成。

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：因為停用的訂用帳戶而造成擱置

### <a name="alert-message"></a>警示訊息

*受控網域已擱置，因為與此網域相關聯的 Azure 訂用帳戶不在作用中。*

### <a name="resolution"></a>解決方案

> [!WARNING]
> 如果 Azure AD DS 託管域被掛起很長時間,則有將其刪除的危險。 儘快解決掛起的原因。 有關詳細資訊,請參閱瞭解[Azure AD DS 的掛起狀態](suspension.md)。

Azure AD DS 需要活動訂閱。 如果 Azure AD DS 託管域關聯的 Azure 訂閱未處於活動狀態,則必須續訂該訂閱以重新啟動訂閱。

1. [更新 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 續訂訂閱後,Azure AD DS 通知允許您重新啟用託管域。

再次啟用託管域後,Azure AD DS 託管域的運行狀況會在兩小時內自動更新並刪除警報。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：因為無效的組態而造成擱置

### <a name="alert-message"></a>警示訊息

*託管域由於配置無效而掛起。長期以來,該服務無法管理、修補或更新託管域的域控制器。*

### <a name="resolution"></a>解決方案

> [!WARNING]
> 如果 Azure AD DS 託管域被掛起很長時間,則有將其刪除的危險。 儘快解決掛起的原因。 有關詳細資訊,請參閱瞭解[Azure AD DS 的掛起狀態](suspension.md)。

[檢查 Azure AD DS 運行狀況](check-health.md)中存在指示託管域配置中出現問題的警報。 如果能夠解決指示配置問題的警報,請等待兩個小時,然後檢查以查看同步是否已完成。 準備就緒後,[打開 Azure 支援請求][azure-support]以重新啟用 Azure AD DS 託管域。

## <a name="next-steps"></a>後續步驟

如果仍有問題,[請打開 Azure 支援請求][azure-support]以取得其他故障排除説明。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
