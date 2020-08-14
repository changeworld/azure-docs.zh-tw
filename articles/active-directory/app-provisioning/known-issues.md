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
ms.date: 08/12/2020
ms.reviewer: arvinh
ms.openlocfilehash: 127629cb0102c2736995364db9202cd837d99a17
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214275"
---
# <a name="known-issues-application-provisioning"></a>已知問題：應用程式布建
使用應用程式布建時要注意的已知問題。 您可以在 UserVoice 上提供有關應用程式布建服務的意見反應，請參閱 [Azure AD 應用程式](https://aka.ms/appprovisioningfeaturerequest)布建 UserVoice。 我們密切監看 UserVoice，讓我們可以改善服務。 

> [!NOTE]
> 這不是已知問題的完整清單。 如果您知道未列出的問題，請在頁面底部提供意見反應。

## <a name="authorization"></a>授權 

**連線測試成功之後無法儲存**

如果您可以成功測試連線，但無法儲存，則表示您已超過允許的認證儲存體限制。 若要深入瞭解，請參閱 [儲存系統管理員認證時發生問題](application-provisioning-config-problem-storage-limit.md)。

**無法儲存**

必須填入租使用者 URL、秘密權杖和通知電子郵件，才能儲存。 您無法只提供其中一個。 

**無法將布建模式變更回手動**

第一次設定布建之後，您會注意到布建模式已從手動切換為自動。 您無法將它變更回手動。 但您可以透過 UI 關閉布建。 關閉 UI 中的布建實際上等同于將下拉式清單設定為 [手動]。  


## <a name="attribute-mappings"></a>屬性對應 

**屬性 SamAccountName 或 userType 無法當做來源屬性使用**

根據預設，屬性 SamAccountName 和 userType 無法當做來源屬性使用。 擴充您的架構以新增屬性。 您可以藉由擴充您的架構，將屬性新增至可用來源屬性的清單。 若要深入瞭解，請參閱 [遺失來源屬性](user-provisioning-sync-attributes-for-mapping.md)。 

**架構延伸模組的來源屬性下拉式清單遺失**

您架構的延伸模組有時可能會從 UI 的 [來源屬性] 下拉式清單中遺漏。 進入屬性對應的 [advanced] 設定，並手動加入屬性。 若要深入瞭解，請參閱 [自訂屬性](customize-application-attributes.md)對應。

**無法布建 Null 屬性**

Azure AD 目前無法提供 null 屬性。 如果使用者物件上的屬性為 null，則會略過。 

**屬性對應運算式的最大字元數**

屬性對應運算式最多可以有10000個字元。 


## <a name="service-issues"></a>服務問題 

**不支援的案例**

- 不支援布建密碼。 
- 不支援布建的嵌套群組。 
- 因為租使用者的大小，所以不支援布建至 B2C 租使用者。 

**變更不會從目標應用程式移至 Azure AD**

應用程式布建服務不知道在外部應用程式中所做的變更。 因此，不會採取任何動作來復原。 應用程式布建服務會依賴在 Azure AD 中進行的變更。  

**布建週期會持續到完成為止**

設定布 `enabled = off` 建或叫用停止時，目前的布建週期會繼續執行，直到完成為止。 服務將會停止執行任何未來的迴圈，直到您再次開啟布建。

**未布建群組的成員**

當群組在範圍內且成員超出範圍時，就會布建群組。 不會布建超出範圍的使用者。 如果成員回到範圍內，服務將不會立即偵測到變更。 重新開機布建將會解決此問題。 我們建議定期重新開機服務，以確保所有使用者都已正確布建。  


## <a name="next-steps"></a>後續步驟
- [佈建運作方式](how-provisioning-works.md)
