---
title: Azure Active Directory 新增和驗證自訂網域名稱 | Microsoft Docs
description: Azure Active Directory 中管理網域的管理概念和做法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/20/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd77b1d82fc427f53a251ef66230b450c1060fd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547571"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>管理 Azure Active Directory 中的自訂網域名稱

功能變數名稱是許多 Azure Active Directory (Azure AD) 資源之識別碼的重要部分：它是使用者的使用者名稱或電子郵件地址的一部分、群組位址的一部分，而且有時是應用程式之應用程式識別碼 URI 的一部分。 Azure AD 中的資源可以包含包含資源之組織所擁有的功能變數名稱。 只有全域管理員可以在 Azure AD 中管理網域。

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>為您的 Azure AD 組織設定主功能變數名稱稱

當您的組織建立時，初始功能變數名稱（例如 ' contoso.onmicrosoft.com '）也是主功能變數名稱稱。 當您建立新的使用者時，主要網域即為新使用者的預設網域名稱。 設定主要網域名稱可以簡化系統管理員在入口網站中建立新使用者的程序。 變更主要網域名稱：

1. 使用組織的全域管理員帳戶登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 **Azure Active Directory**。
3. 選取 [自訂網域名稱]。
  
   ![開啟使用者管理頁面](./media/domains-manage/add-custom-domain.png)
4. 選取您想要設為主要網域的網域名稱。
5. 選取 [設為主要] 命令。 出現提示時，請確認您的選擇。
  
   ![將網域名稱設為主要網域名稱](./media/domains-manage/make-primary-domain.png)

您可以將組織的主功能變數名稱稱變更為任何未同盟的已驗證自訂網域。 變更您組織的主域不會變更任何現有使用者的使用者名稱。

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>新增自訂網域名稱到您的 Azure AD 組織

您最多可以新增 900 個受控網域名稱。 如果您要設定與內部部署 Active Directory 同盟的所有網域，您最多可以在每個組織中新增450個功能變數名稱。

## <a name="add-subdomains-of-a-custom-domain"></a>新增自訂網域的子網域

如果您想要將子功能變數名稱稱（例如 ' europe.contoso.com '）新增至您的組織，您應該先新增並驗證根域，例如 contoso.com。 Azure AD 會自動驗證子網域。 若要查看您新增的子網域是否通過驗證，請重新整理瀏覽器中的網域清單。

如果您已將 contoso.com 網域新增至一個 Azure AD 組織，也可以驗證不同 Azure AD 組織中的子域 europe.contoso.com。 加入子域時，系統會提示您在 DNS 主控提供者中新增 TXT 記錄。



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>如果您變更您的自訂網域名稱的 DNS 註冊機構，該怎麼辦

如果您變更 DNS 註冊機構，則 Azure AD 中就沒有額外的組態工作。 您可以繼續使用 Azure AD 的網域名稱，不需中斷。 如果您使用自訂功能變數名稱搭配 Microsoft 365、Intune，或依賴 Azure AD 中自訂功能變數名稱的其他服務，請參閱這些服務的檔。

## <a name="delete-a-custom-domain-name"></a>刪除自訂網域名稱

如果貴組織不再使用該網域名稱，或如果您需要在另一個 Azure AD 中使用該網域名稱，您可以從您的 Azure AD 刪除自訂網域名稱。

若要刪除自訂功能變數名稱，您必須先確定您的組織中沒有任何資源依賴功能變數名稱。 如果有下列情況，您就無法從組織中刪除功能變數名稱：

* 有任何使用者的使用者名稱、電子郵件地址或 Proxy 位址包含該網域名稱。
* 有任何群組的電子郵件地址或 Proxy 位址包含該網域名稱。
* Azure AD 中任何應用程式的應用程式識別碼 URI 包括該網域名稱。

您必須在 Azure AD 組織中變更或刪除任何這類資源，才能刪除自訂功能變數名稱。

### <a name="forcedelete-option"></a>ForceDelete 選項

您可以 **ForceDelete**[Azure AD 管理中心](https://aad.portal.azure.com)中的網域名稱，或使用 [Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)。 這些選項會使用非同步作業，並更新所有從自訂網域名稱 (例如「user@contoso.com」) 到初始預設網域名稱 (例如「user@contoso.onmicrosoft.com」) 的參考。

若要在 Azure 入口網站呼叫 **ForceDelete**，您必須確保網域名稱擁有低於 1000 個參考，且必須更新或移除 [Exchange 管理中心](https://outlook.office365.com/ecp/)的所有以 Exchange 作為佈建服務的參考。 這包括 Exchange 已啟用郵件的安全性群組和分散式清單；如需詳細資訊，請參閱[移除已啟用郵件的安全性群組](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups&preserve-view=true)。 此外，如果下列任一項為真，**ForceDelete** 作業就會失敗：

* 您已透過 Microsoft 365 網域訂用帳戶服務購買網域
* 您是合作夥伴，代表其他客戶組織進行管理

下列動作為執行 **ForceDelete** 作業的一部分：

* 使用自訂網域名稱參考，將 UPN、 EmailAddress 和 ProxyAddress 重新命名為初始預設網域名稱。
* 使用自訂網域名稱參考，將群組 EmailAddress 重新命名為初始預設網域名稱。
* 使用自訂網域名稱參考，將應用程式 identifierUri 重新命名為初始預設網域名稱。

當發生以下情況時，會傳回錯誤訊息：

* 要重新命名的物件數目大於 1000
* 其中一個要重新命名的應用程式是多租用戶應用程式

### <a name="frequently-asked-questions"></a>常見問題集

**問：為什麼網域刪除失敗時，會收到錯誤訊息，表示我在這個網域名稱中擁有由 Exchange 主控的群組？** <br>
**答：** 目前，已啟用郵件安全性群組和分散式清單等特定群組由 Exchange 佈建，且需在 [Exchange 管理中心 (EAC)](https://outlook.office365.com/ecp/) 手動清除。 可能會有停留的 ProxyAddresses 仰賴自訂網域名稱，且需要手動更新為其他網域名稱。 

**問：我已使用 admin\@contoso.com 身分登入，但為什麼我無法刪除網域名稱 “contoso.com”？**<br>
**答：** 您不能參考您在使用者帳戶名稱中嘗試刪除的自訂網域名稱。 請確認全域管理員帳戶使用初始預設網域名稱 (.onmicrosoft.com)，例如 admin@contoso.onmicrosoft.com。 使用不同的全域管理員帳戶 (例如 admin@contoso.onmicrosoft.com) 或其他帳戶是 admin@fabrikam.com 的自訂網域名稱 (例如「fabrikam.com」) 登入。

**問：我已按下 [刪除網域] 按鈕，並看到刪除作業的`In Progress`狀態。需要多久的時間？如果失敗，會發生什麼事？**<br>
**答：** 刪除網域作業是非同步的背景工作，會將所有操考重新命名為網域名稱。 此作業應會在數分鐘內完成。 如果網域刪除失敗，請確定您不擁有：

* 使用 appIdentifierURI 設定網域名稱的應用程式
* 任何參考自訂網域名稱的已啟用郵件群組
* 超過 1000 個參考的網域名稱

如果您發現任何不符合的條件，請手動清除參考，並再次嘗試刪除網域。

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>使用 PowerShell 或 Microsoft Graph API 來管理網域名稱

在 Azure Active Directory 中，大部分的網域名稱管理工作也都可以使用 Microsoft PowerShell 來完成，或使用 Microsoft Graph API 以程式設計方式來完成。

* [使用 PowerShell 管理 Azure AD 中的網域名稱](/powershell/module/azuread/?view=azureadps-2.0#domains&preserve-view=true)
* [網域資源類型](/graph/api/resources/domain?view=graph-rest-1.0&preserve-view=true) \(英文\)

## <a name="next-steps"></a>後續步驟

* [新增自訂網域名稱](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [在 Azure AD 中的自訂網域名稱上移除 Exchange 管理中心的 Exchange 已啟用郵件安全性群組](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [使用 Microsoft Graph API 以強制刪除自訂網域名稱](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)