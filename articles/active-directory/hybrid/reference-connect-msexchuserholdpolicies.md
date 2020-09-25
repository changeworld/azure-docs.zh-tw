---
title: Azure AD Connect： msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies |Microsoft Docs
description: 本主題說明 msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies 屬性的屬性行為
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a5a4659671f8d4ded64a4f04f84abf1f67d8825
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265250"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies
下列參考檔說明 Exchange 使用的這些屬性，以及編輯預設同步處理規則的適當方式。

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>MsExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies 是什麼？
有兩種類型的 [保留](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) 可供 Exchange 伺服器使用：訴訟保存和就地保存。 若已啟用訴訟保存，所有信箱都會保留在保存中。  就地保存僅用來保留符合您使用就地電子檔探索工具所定義之搜尋查詢準則的專案。

MsExchUserHoldPolcies 和 cloudMsExchUserHoldPolicies 屬性可讓內部部署 AD 和 Azure AD 根據使用者是在線上使用內部部署 Exchange 或 Exchange，判斷哪些使用者正在進行保存。

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies 同步處理流程
依預設，MsExchUserHoldPolcies 會 Azure AD Connect 直接同步處理到元處理中的 msExchUserHoldPolicies 屬性，然後再到 Azure AD 中的 msExchUserHoldPolicies 屬性。

下表說明流程：

從內部部署 Active Directory 的輸入：

|Active Directory 屬性|屬性名稱|流程類型|元屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|內部部署 Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|In from AD-使用者 Exchange|

輸出至 Azure AD：

|元屬性|屬性名稱|流程類型|Azure AD 屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|Out 至 AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies 同步處理流程
依預設，cloudMsExchUserHoldPolicies 會 Azure AD Connect 直接同步處理到元處理中的 cloudMsExchUserHoldPolicies 屬性。 然後，如果元 msExchUserHoldPolicies 中的不是 null，就會將屬性流動至 Active Directory。

下表說明流程：

來自 Azure AD 的輸入：

|Active Directory 屬性|屬性名稱|流程類型|元屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|內部部署 Active Directory|cloudMsExchUserHoldPolicies|直接|cloudMsExchUserHoldPolicies|從 AAD-使用者交換|

輸出至內部部署 Active Directory：

|元屬性|屬性名稱|流程類型|Azure AD 屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|如果 (不是 Null) |msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>屬性行為的資訊
MsExchangeUserHoldPolicies 是單一授權單位屬性。  您可以在此案例中的物件上設定單一授權屬性 (在此案例中，使用者物件) 在內部部署目錄或雲端目錄中。  授權起始規則的指示，如果屬性從內部部署同步處理，則不允許 Azure AD 更新此屬性。

若要允許使用者在雲端中的使用者物件上設定保留原則，請使用 cloudMSExchangeUserHoldPolicies 屬性。 使用這個屬性的原因是 Azure AD 無法根據上述規則來直接設定 msExchangeUserHoldPolicies。  如果 msExchangeUserHoldPolicies 不是 null，則這個屬性會接著同步處理回內部部署目錄，並取代 msExchangeUserHoldPolicies 目前的值。

比方說，在某些情況下，如果兩者同時在內部部署和 Azure 中變更，這可能會導致一些問題。  

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。