---
title: Azure Active Directory 新增和驗證自訂網域名稱 | Microsoft Docs
description: Azure Active Directory 中管理網域的管理概念和做法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77559228"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>管理 Azure Active Directory 中的自訂網域名稱

網域名稱是許多目錄資源識別項重要的一部分：它可能是使用者的使用者名稱或電子郵件地址的一部分、群組位址的一部分，有時也可能是應用程式的應用程式識別碼 URI 的一部分。 Azure Active Directory (Azure AD) 中的資源可包含目錄 (其中含有資源) 所擁有的網域名稱。 只有全域管理員可以在 Azure AD 中管理網域。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>設定 Azure AD 目錄的主要網域名稱

建立目錄時，初始網域名稱 (例如 ‘contoso.onmicrosoft.com’) 同時也是主要網域名稱。 當您建立新的使用者時，主要網域即為新使用者的預設網域名稱。 設定主要網域名稱可以簡化系統管理員在入口網站中建立新使用者的程序。 變更主要網域名稱：

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **Azure Active Directory**。
3. 選取 [自訂網域名稱]****。
  
   ![開啟 [使用者管理] 頁面](./media/domains-manage/add-custom-domain.png)
4. 選取您想要設為主要網域的網域名稱。
5. 選取 [設為主要]**** 命令。 出現提示時，請確認您的選擇。
  
   ![將功能變數名稱設為主要名稱](./media/domains-manage/make-primary-domain.png)

您可以將目錄的主要網域名稱變更為任何已驗證的非同盟自訂網域。 變更目錄的主要網域時，並不會變更任何現有使用者的使用者名稱。

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>為 Azure AD 租用戶新增自訂網域名稱

您最多可以新增 900 個受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則最多可以在每個目錄中新增 450 個網域名稱。

## <a name="add-subdomains-of-a-custom-domain"></a>新增自訂網域的子網域

如果您想要將第三層網域名稱新增至您的目錄，例如 'europe.contoso.com'，則應先新增並驗證第二層網域，例如 contoso.com。 Azure AD 會自動驗證子網域。 若要查看您新增的子網域是否通過驗證，請重新整理瀏覽器中的網域清單。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>如果您變更您的自訂網域名稱的 DNS 註冊機構，該怎麼辦

如果您變更 DNS 註冊機構，則 Azure AD 中就沒有額外的組態工作。 您可以繼續使用 Azure AD 的網域名稱，不需中斷。 如果您的自訂網域名稱搭配使用 Office 365、Intune 或仰賴 Azure AD 自訂網域名稱的其他服務，則請參閱這些服務的文件。

## <a name="delete-a-custom-domain-name"></a>刪除自訂網域名稱

如果貴組織不再使用該網域名稱，或如果您需要在另一個 Azure AD 中使用該網域名稱，您可以從您的 Azure AD 刪除自訂網域名稱。

若要刪除自訂網域名稱，您必須先確定目錄中沒有任何資源仰賴該網域名稱。 如果有下列情況，即無法刪除目錄的網域名稱：

* 有任何使用者的使用者名稱、電子郵件地址或 Proxy 位址包含該網域名稱。
* 有任何群組的電子郵件地址或 Proxy 位址包含該網域名稱。
* Azure AD 中任何應用程式的應用程式識別碼 URI 包括該網域名稱。

您必須變更或刪除 Azure AD 目錄中任何這類資源，才能刪除自訂網域名稱。

### <a name="forcedelete-option"></a>ForceDelete 選項

您可以 **ForceDelete**[Azure AD 管理中心](https://aad.portal.azure.com)中的網域名稱，或使用 [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)。 這些選項會使用非同步作業，並更新所有從自訂網域名稱 (例如「user@contoso.com」) 到初始預設網域名稱 (例如「user@contoso.onmicrosoft.com」) 的參考。 

若要在 Azure 入口網站呼叫 **ForceDelete**，您必須確保網域名稱擁有低於 1000 個參考，且必須更新或移除 [Exchange 管理中心](https://outlook.office365.com/ecp/)的所有以 Exchange 作為佈建服務的參考。 這包括 Exchange 已啟用郵件的安全性群組和分散式清單；如需詳細資訊，請參閱[移除已啟用郵件的安全性群組](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)。 此外，如果下列任一項為真，**ForceDelete** 作業就會失敗：

* 您透過 Office 365 網域訂用帳戶服務購買網域
* 您是合作夥伴，代表其他客戶租用戶進行管理

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

**問：我是以系統管理員\@身分登入 contoso.com，但我無法刪除功能變數名稱 "contoso.com"？**<br>
**答：** 您不能參考您在使用者帳戶名稱中嘗試刪除的自訂網域名稱。 請確認全域管理員帳戶使用初始預設網域名稱 (.onmicrosoft.com)，例如 admin@contoso.onmicrosoft.com。 使用不同的全域管理員帳戶 (例如 admin@contoso.onmicrosoft.com) 或其他帳戶是 admin@fabrikam.com 的自訂網域名稱 (例如「fabrikam.com」) 登入。

**問：我已按下 [刪除網域] `In Progress`按鈕，並看到刪除作業的狀態。需要多久的時間？如果失敗，會發生什麼事？**<br>
**答：** 刪除網域作業是非同步的背景工作，會將所有操考重新命名為網域名稱。 此作業應會在數分鐘內完成。 如果網域刪除失敗，請確定您不擁有：

* 使用 appIdentifierURI 設定網域名稱的應用程式
* 任何參考自訂網域名稱的已啟用郵件群組
* 超過 1000 個參考的網域名稱

如果您發現任何不符合的條件，請手動清除參考，並再次嘗試刪除網域。

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>使用 PowerShell 或 Microsoft Graph API 來管理功能變數名稱

在 Azure Active Directory 中，大部分的功能變數名稱管理工作也可以使用 Microsoft PowerShell 來完成，或以程式設計方式使用 Microsoft Graph API。

* [使用 PowerShell 管理 Azure AD 中的網域名稱](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [網域資源類型](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>後續步驟

* [新增自訂網域名稱](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [在 Azure AD 中的自訂網域名稱上移除 Exchange 管理中心的 Exchange 已啟用郵件安全性群組](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [使用 Microsoft Graph API 以強制刪除自訂網域名稱](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
