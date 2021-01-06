---
title: Azure AD 中的應用程式布建已知問題
description: 瞭解在 Azure AD 中使用自動化應用程式布建時的已知問題。
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: bde5cecc4052724f795cac03f6efceb3451ea2ee
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914343"
---
# <a name="known-issues-application-provisioning"></a>已知問題：應用程式布建
使用應用程式布建時要注意的已知問題。 您可以在 UserVoice 上提供關於應用程式布建服務的意見反應，請參閱 [Azure AD 應用程式](https://aka.ms/appprovisioningfeaturerequest)布建 UserVoice。 我們會密切觀賞 UserVoice，讓我們可以改善服務。 

> [!NOTE]
> 這不是完整的已知問題清單。 如果您知道未列出的問題，請在頁面底部提供意見反應。

## <a name="authorization"></a>授權 

**連接測試成功後無法儲存**

如果您可以成功測試連接但無法儲存，則表示您已超過允許的認證儲存空間限制。 若要深入瞭解，請參閱 [儲存系統管理員認證時發生問題](./user-provisioning.md)。

**無法儲存**

您必須填寫租使用者 URL、秘密權杖和通知電子郵件，才能儲存。 您無法只提供其中一個。 

**無法將布建模式變更回手動**

當您第一次設定布建之後，您會注意到布建模式已從手動切換為自動。 您無法將它變更回手動。 但是您可以透過 UI 關閉布建。 在 UI 中關閉布建實際上與將下拉式清單設定為手動一樣。  


## <a name="attribute-mappings"></a>屬性對應 

**屬性 SamAccountName 或 userType 無法作為來源屬性使用**

屬性 SamAccountName 和 userType 預設無法作為來源屬性。 擴充您的架構以新增屬性。 您可以藉由擴充您的架構，將屬性新增至可用的來源屬性清單。 若要深入瞭解，請參閱 [遺失來源屬性](user-provisioning-sync-attributes-for-mapping.md)。 

**架構延伸缺少來源屬性下拉式清單**

在 UI 的 [來源屬性] 下拉式清單中，有時可能會遺漏架構的延伸。 移至屬性對應的 [advanced] 設定，並手動加入屬性。 若要深入瞭解，請參閱 [自訂屬性](customize-application-attributes.md)對應。

**無法布建 Null 屬性**

Azure AD 目前無法布建 null 屬性。 如果使用者物件上的屬性為 null，則會略過該屬性。 

**屬性對應運算式的最大字元數**

屬性對應運算式最多可以有10000個字元。 

**不支援的範圍篩選**

目錄延伸模組、appRoleAssignments、userType 和 accountExpires 不支援做為範圍篩選器。


## <a name="service-issues"></a>服務問題 

**不支援的案例**

- 不支援提供密碼。 
- 不支援布建嵌套群組。 
- 因為租使用者的大小，所以不支援布建至 B2C 租使用者。
- 並非所有的布建應用程式都可在所有雲端中使用。 例如，Atlassian 還無法在政府雲端中使用。 我們正在與應用程式開發人員合作，將其應用程式上架到所有雲端。

**我的 OIDC 應用程式無法使用自動布建**

如果您建立應用程式註冊，將不會啟用企業應用程式中對應的服務主體來自動布建使用者。 您必須將應用程式新增至資源庫（如果預定供多個組織使用），或建立第二個非資源庫應用程式來進行布建。 

**布建間隔是固定的**

布建週期之間的 [時間](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) 目前無法設定。 

**變更不會從目標應用程式移至 Azure AD**

應用程式布建服務不會察覺在外部應用程式中所做的變更。 因此，不會採取任何動作來復原。 應用程式布建服務依賴 Azure AD 中所做的變更。 

**從同步處理切換至已指派的同步處理無法運作**

將範圍從「同步全部」變更為「已指派同步處理」之後，請務必同時執行重新開機，以確保變更生效。 您可以從 UI 重新開機。

**布建週期會持續到完成為止**

當設定布 `enabled = off` 建或按下停止時，目前的布建週期將會繼續執行，直到完成為止。 服務將會停止執行任何未來的週期，直到您再次開啟布建。

**未布建群組的成員**

當群組在範圍內，而成員超出範圍時，就會布建群組。 不會布建超出範圍的使用者。 如果成員回到範圍中，服務將不會立即偵測變更。 重新開機布建將會解決此問題。 建議您定期重新開機服務，以確保所有使用者都已正確布建。  

**未布建管理員**

如果使用者和其管理員都在布建範圍內，則服務會布建使用者，然後更新管理員。 不過，如果使用者在第一天是在範圍內，而管理員超出範圍，我們將會在不使用經理參考的情況下布建使用者。 當管理員進入範圍內時，除非您重新開機布建，並再次讓服務重新評估所有使用者，否則管理員參考將不會更新。 

## <a name="next-steps"></a>後續步驟
- [佈建運作方式](how-provisioning-works.md)
