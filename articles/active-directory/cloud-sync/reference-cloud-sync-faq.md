---
title: Azure AD Connect cloud sync 常見問題
description: 本檔說明雲端同步處理的常見問題。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613094"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Azure Active Directory Connect cloud sync 常見問題

瞭解 Azure Active Directory (Azure AD) Connect cloud sync 的常見問題。

## <a name="general-installation"></a>一般安裝

**問：雲端同步的執行頻率為何？**

雲端佈建排程為每 2 分鐘執行一次。 每隔 2 分鐘，就會將任何使用者、群組和密碼雜湊變更佈建到 Azure AD。

**問：在第一次執行時看到密碼雜湊同步處理失敗。為什麼？**

這是預期行為。 失敗的原因是使用者物件不存在於 Azure AD 中。 使用者佈建到 Azure AD 後，應該會接著執行密碼雜湊的佈建。 請等候幾次執行，並確認密碼雜湊同步作業不再發生錯誤。

**問：如果 Active Directory 實例具有雲端同步 (不支援的屬性（例如目錄延伸) ），會發生什麼事？**

雲端佈建將會執行及佈建支援的屬性。 不支援的屬性將不會佈建到 Azure AD。 請參閱 Active Directory 中的目錄延伸，並確定您不需要將這些屬性傳送至 Azure AD。 如果需要一個或多個屬性，請考慮使用 Azure AD Connect 同步，或將所需的資訊移至其中一個支援的屬性 (例如，延伸模組屬性 1-15)。

**問： Azure AD Connect 同步與雲端同步之間有何差異？**

使用 Azure AD Connect 同步時，會在內部部署同步伺服器上執行佈建。 組態會儲存在內部部署同步伺服器上。 使用 Azure AD Connect cloud sync 時，布建設定會儲存在雲端，並在雲端中作為 Azure AD 布建服務的一部分執行。 

**問：我可以使用雲端同步從多個 Active Directory 樹系同步嗎？**

可以。 您可以使用雲端佈建從多個 Active Directory 樹系進行同步處理。 在多樹系環境中，所有參考 (例如管理員) 都必須位於網域內。  

**問：代理程式如何更新？**

Microsoft 會自動升級代理程式。 對於 IT 小組而言，這會減輕其必須測試和驗證新代理程式版本的負擔。 

**問：我可以停用自動升級嗎？**

目前不支援停用自動升級。

**問：我可以變更雲端同步的來源錨點嗎？**

雲端同步預設會使用 ms ds 一致性 GUID，並以來源錨點的形式回到 ObjectGUID。 目前不支援變更來源錨點。

**問：我在使用雲端同步時，看到 AD 功能變數名稱 (s 的新服務主體) 。是否預期？**

是，雲端同步會為布建設定建立服務主體，並以功能變數名稱作為服務主體名稱。 請勿對服務主體組態進行任何變更。

**問：當同步處理的使用者在下次登入時需要變更密碼時，會發生什麼事？**

如果已在雲端同步中啟用密碼雜湊同步處理，且已同步處理的使用者在內部部署 AD 中的下一次登入時需要變更密碼，則雲端同步處理不會將「變更」密碼雜湊布建至 Azure AD。 使用者變更密碼後，就會將使用者密碼雜湊從 AD 佈建到 Azure AD。

**問：雲端同步是否支援任何物件的 consistencyGUID 回寫？**

否，雲端同步不支援對任何物件 (（包括使用者物件) ）的 consistencyGUID 回寫。 

**問：我正在使用雲端同步處理來布建使用者。我已刪除設定。為什麼我在 Azure AD 中仍會看到舊的已同步處理物件？** 

當您刪除設定時，雲端同步處理不會自動移除 Azure AD 中已同步處理的物件。 若要確保不會有舊物件，請將組態的範圍變更為空的群組或組織單位。 佈建執行並清除物件後，請停用並刪除組態。 

**問：這表示不支援 Exchange 混合式嗎？**

Exchange 混合式部署功能可讓您同時存在內部部署和 Microsoft 365 中的 Exchange 信箱。 Azure AD Connect 會將一組特定的屬性從 Azure AD 同步處理回內部部署目錄。  雲端布建代理程式目前不會將這些屬性同步處理回您的內部部署目錄，因此不支援做為 Azure AD Connect 的取代。

**問：我可以在 Windows Server Core 上安裝雲端布建代理程式嗎？**

否，不支援在 server core 上安裝代理程式。

**問：是否可以搭配使用預備伺服器與雲端布建代理程式？**

否，不支援暫存伺服器。

**問：我可以同步處理來賓使用者帳戶嗎？**

否，不支援同步處理來賓使用者帳戶。

**問：如果我將使用者從雲端同步範圍的 OU 移至範圍為 Azure AD Connect 的 OU，會發生什麼事？**

使用者將會被刪除並重新建立。  從雲端同步範圍的 OU 移動使用者，將會被視為刪除操作。  如果使用者移至受 Azure AD Connect 管理的 OU，則會重新布建至 Azure AD，並建立新的使用者。

**問：如果我重新命名或移動雲端同步篩選範圍內的 OU，Azure AD 中建立的使用者會發生什麼事？**

不做任何動作。  如果重新命名或移動 OU，則不會刪除使用者。

**問： Azure AD Connect cloud sync 是否支援大型群組？**

可以。 今天我們最多支援使用 OU 範圍篩選來同步處理50K 群組成員。 同時，當您使用群組範圍篩選時，建議您將群組大小保留到小於1500的成員。 原因是，即使您可以將大型群組同步處理為群組範圍篩選準則的一部分，當您將成員新增到批次大於1500的群組時，差異同步處理將會失敗。 

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
