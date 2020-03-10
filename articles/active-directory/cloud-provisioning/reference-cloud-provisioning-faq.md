---
title: Azure AD Connect 雲端佈建常見問題集
description: 本文件說明雲端佈建的常見問題。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916569"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect 常見問題集

閱讀 Azure Active Directory (Azure AD) Connect 雲端佈建的常見問題。

## <a name="general-installation"></a>一般安裝

**問：雲端佈建的執行頻率為何？**

雲端佈建排程為每 2 分鐘執行一次。 每隔 2 分鐘，就會將任何使用者、群組和密碼雜湊變更佈建到 Azure AD。

**問：第一次執行時發生密碼雜湊同步失敗的狀況。原因為何？**

這是預期行為。 失敗的原因是使用者物件不存在於 Azure AD 中。 使用者佈建到 Azure AD 後，應該會接著執行密碼雜湊的佈建。 請等候幾次執行，並確認密碼雜湊同步作業不再發生錯誤。

**問：如果 Active Directory 執行個體具有雲端佈建不支援的屬性 (例如目錄延伸模組)，會發生什麼事？**

雲端佈建將會執行及佈建支援的屬性。 不支援的屬性將不會佈建到 Azure AD。 請參閱 Active Directory 中的目錄延伸模組，並確定您不需要讓這些屬性流向 Azure AD。 如果需要一個或多個屬性，請考慮使用 Azure AD Connect 同步，或將所需的資訊移至其中一個支援的屬性 (例如，延伸模組屬性 1-15)。

**問：Azure AD Connect 同步與雲端佈建之間有何差異？**

使用 Azure AD Connect 同步時，會在內部部署同步伺服器上執行佈建。 組態會儲存在內部部署同步伺服器上。 使用 Azure AD Connect 雲端佈建時，佈建組態會儲存在雲端中，並在 Azure AD 佈建服務執行的過程中於雲端中執行。 

**問：我是否可使用雲端佈建從多個 Active Directory 樹系進行同步處理？**

是。 您可以使用雲端佈建從多個 Active Directory 樹系進行同步處理。 在多樹系環境中，所有參考 (例如管理員) 都必須位於網域內。  

**問：如何更新代理程式？**

Microsoft 會自動升級代理程式。 對於 IT 小組而言，這會減輕其必須測試和驗證新代理程式版本的負擔。 

**問：是否可以停用自動升級？**

目前不支援停用自動升級。

**問：是否可以變更雲端佈建的來源錨點？**

根據預設，雲端佈建會使用 ms-ds-consistency-GUID 作為來源錨點，並且可回復為 ObjectGUID。 目前不支援變更來源錨點。

**問：在使用雲端佈建時，我看到具有 AD 網域名稱的新服務主體。這是正常行為嗎？**

是，雲端佈建會使用網域名稱作為服務主體名稱，為佈建組態建立服務主體。 請勿對服務主體組態進行任何變更。

**問：如果已同步的使用者在下次登入時需要變更密碼，會發生什麼情況？**

如果已在雲端佈建中啟用密碼雜湊同步處理，且已同步的使用者在內部部署 AD 中的下一次登入時需要變更密碼，則雲端佈建不會將要變更的密碼雜湊佈建到 Azure AD。 使用者變更密碼後，就會將使用者密碼雜湊從 AD 佈建到 Azure AD。

**問：雲端佈建是否支援任何物件的 ms-ds-consistencyGUID 回寫？**

否，雲端佈建不支援任何物件的 ms-ds-consistencyGUID 回寫 (包括使用者物件)。 

**問：我使用雲端佈建進行使用者的佈建。我已刪除組態。為何我仍在 Azure AD 中看到已同步的舊物件？** 

當您刪除組態時，雲端佈建並不會清除 Azure AD 中已同步的物件。 若要確保不會有舊物件，請將組態的範圍變更為空的群組或組織單位。 佈建執行並清除物件後，請停用並刪除組態。 

**問：Exchange 混合不受支援是什麼意思？**

Exchange 混合部署功能允許在內部部署和 Office 365 中並存 Exchange 信箱。 Azure AD Connect 會將一組特定的屬性從 Azure AD 同步處理回內部部署目錄。  雲端佈建代理程式目前不會將這些屬性同步處理回您的內部部署目錄，因此不支援使用該代理程式來取代 Azure AD Connect。

**問：我是否可在 Windows Server Core 上安裝雲端佈建代理程式？**

否，不支援在 Server Core 上安裝代理程式。

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
