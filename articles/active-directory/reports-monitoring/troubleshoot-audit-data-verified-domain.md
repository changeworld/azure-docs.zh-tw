---
title: 針對已驗證網域變更的審查資料進行疑難排解 |Microsoft Docs
description: 當您變更使用者驗證的網域時，會提供您在 Azure Active Directory 活動記錄中顯示的資訊。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117424"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>疑難排解：已驗證的網域變更時的審核資料 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>我對我的使用者有很多變更，我不確定它的原因為何。

### <a name="symptoms"></a>徵狀

我檢查 Azure AD 的 audit 記錄，並查看我的 Azure AD 租使用者中發生多個使用者更新。 這些**更新使用者**事件不**會顯示動作**專案資訊，這會造成不確定性的情況，也就是觸發對使用者進行大量變更的物件。 

### <a name="cause"></a>原因

 大量物件變更的常見原因是名為**ProxyCalc**的非同步後端作業。  **ProxyCalc**是用來判斷在 Azure AD 使用者、群組或連絡人中更新之適當**UserPrincipalName**和**Proxy 位址**的邏輯。 **ProxyCalc**背後的設計是確保所有**UserPrincipalName**和**Proxy 位址**在 Azure AD 隨時都一致。 **ProxyCalc**必須由明確變更（如已驗證的網域變更）觸發，而且不會永久在背景中以工作的形式執行。 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>UserPrincipalName 一致性是什麼意思？ 

對於僅限雲端的使用者，一致性表示**UserPrincipalName**設定為已驗證的網域尾碼。 當處理不一致的**UserPrincipalName**時， **ProxyCalc**會將它轉換成預設 onmicrosoft.com 尾碼，例如：username@Contoso.onmicrosoft.com 

針對已同步處理的使用者，一致性表示**UserPrincipalName**設定為已驗證的網域尾碼，並符合內部部署的**UserPrincipalName**值（ShadowUserPrincipalName）。 當處理不一致的**UserPrincipalName**時， **ProxyCalc**會還原為與**ShadowUserPrincipalName**相同的值，或者，如果已從租使用者中移除網域尾碼，則會將它轉換成預設的 *. onmicrosoft.com 網域尾碼。 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Proxy 位址一致性是什麼意思？ 

對於僅限雲端的使用者，一致性表示 Proxy 位址符合已驗證的網域尾碼。 當處理不一致的 Proxy 位址時， **ProxyCalc**會將它轉換成預設的 *. onmicrosoft.com 網域尾碼，例如：SMTP:username@Contoso.onmicrosoft.com 

針對同步處理的使用者，一致性表示 Proxy 位址符合內部部署 Proxy 位址值（亦即 ShadowProxyAddresses). **ProxyAddresses**應該與**ShadowProxyAddresses**同步。 如果同步處理的使用者已指派 Exchange 授權，則 Proxy 位址必須符合內部部署 Proxy 位址值，而且也必須符合已驗證的網域尾碼。 在此案例中， **ProxyCalc**會使用未驗證的網域尾碼來淨化不一致的 Proxy 位址，並將從 Azure AD 中的物件中移除。 如果稍後驗證該未驗證的網域， **ProxyCalc**會重新計算，並將 Proxy 位址從**ShadowProxyAddresses**新增至 Azure AD 中的物件。  

> [!NOTE]
> 針對已同步處理的物件，若要避免**ProxyCalc**邏輯計算非預期的結果，最好將 Proxy 位址設定為內部部署物件上 Azure AD 已驗證的網域。  

  
每次有已驗證的網域變更時，可能會觸發**ProxyCalc**的其中一項管理工作。 每次在 Azure AD 租使用者中新增/移除已驗證的網域時，就會發生這項工作，而這會在內部觸發**ProxyCalc**。  

例如，如果您將已驗證的網域 Fabrikam.com 新增至您的 Contoso.onmicrosoft.com 租使用者，此動作將會在租使用者中的所有物件上觸發 ProxyCalc 操作。 此事件將會在 Azure AD Audit 記錄檔中捕捉為**更新使用者**事件，並在前面加上「**已驗證的網域**」事件。 另一方面，如果 Fabrikam.com 已從 Contoso.onmicrosoft.com 租使用者中移除，則所有**更新使用者**事件的前面會加上「**移除已驗證的網域**」事件。   

#### <a name="additional-notes"></a>其他注意事項：

ProxyCalc 不會對某些物件進行變更： 

- 沒有有效的 Exchange 授權 
- 將**MSExchRemoteRecipientType**設定為 Null 
- 不會視為共用資源。 當**CloudMSExchRecipientDisplayType**包含下列其中一個值時，共用資源是 **： MailboxUser （共用）**、 **PublicFolder**、 **ConferenceRoomMailbox**、 **EquipmentMailbox**、 **ArbitrationMailbox**、 **RoomList**、 **TeamMailboxUser**、**群組信箱**、**排程信箱**、 **ACLableMailboxUser**、 **ACLableTeamMailboxUser** 
  
 為了在這兩個不同的事件之間建立更多相互關聯，Microsoft 正致力於更新 audit 記錄中的動作專案資訊，以識別已驗證的網域**變更所觸發**的這些變更。 此動作將有助於檢查已驗證的網域變更事件何時發生，並已開始大量更新其租使用者中的物件。 

此外，在大部分的情況下，使用者沒有任何變更，因為其**UserPrincipalName**和**Proxy 位址**是一致的，因此我們正努力在 Audit 記錄中顯示導致物件實際變更的更新。 此動作會防止 audit 記錄中出現雜訊，並協助系統管理員將剩餘的使用者變更與已驗證的網域變更事件相互關聯，如上面所述。 

## <a name="next-steps"></a>後續步驟

[Azure AD Connect 同步處理服務陰影屬性](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
