---
title: Azure AD 連接：msExchUserHold 策略和雲 MMsExchUserHold 策略 |微軟文檔
description: 本主題介紹 msExchUserHold 策略和雲MsExchUserHold策略屬性的屬性行為
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213082"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD 連接 - msExchUserHold 策略和雲使用者保留原則
以下參考文檔描述了 Exchange 使用的這些屬性以及編輯預設同步規則的正確方法。

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>什麼是 msExchUserHold 策略和雲使用者保留原則？
交換伺服器有兩種類型的[保留](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019)：訴訟保留和就地保留。 啟用"訴訟保留"後，所有郵箱的所有專案都置於保留狀態。  就地保留僅用於保留那些滿足使用就地電子資料展示工具定義的搜索查詢準則的專案。

MsExchUserHoldPolcies 和 cloudMsExchUserHold 策略屬性允許本地 AD 和 Azure AD 根據使用者是使用本地 Exchange 還是線上 Exchange 來確定處於保留狀態的使用者。

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHold策略同步流
預設情況下，MsExchUserHoldPolcies 通過 Azure AD 直接連接到元節中的 msExchUserHold 策略屬性，然後連接到 Azure AD 中的 msExchUserHoldPols 屬性

下表描述了流：

從本地活動目錄入站：

|活動目錄屬性|屬性名稱|流程類型|元宇宙屬性|同步規則|
|-----|-----|-----|-----|-----|
|內部部署 Active Directory|msExchUserHoldPolicies|直接|msExchUserHold員警|從 AD 輸入 - 使用者交換|

從站到 Azure AD：

|元宇宙屬性|屬性名稱|流程類型|Azure AD 屬性|同步規則|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|出至 AAD = 使用者交換線上|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>雲MExch使用者保留原則同步流
預設情況下，雲MExchUserHold策略由 Azure AD 直接連接到元節中的雲MExchUserHold策略屬性同步。 然後，如果 msExchUserHoldPolices 在元宇宙中不是 null，則屬性將流向活動目錄。

下表描述了流：

從 Azure AD 入站：

|活動目錄屬性|屬性名稱|流程類型|元宇宙屬性|同步規則|
|-----|-----|-----|-----|-----|
|內部部署 Active Directory|雲MExch使用者保留政策|直接|雲MExch使用者保留政策|從 AAD - 使用者交換|

從站到本地活動目錄：

|元宇宙屬性|屬性名稱|流程類型|Azure AD 屬性|同步規則|
|-----|-----|-----|-----|-----|
|Azure Active Directory|雲MExch使用者保留政策|如果（非空）|msExchUserHoldPolicies|出到 AD = 使用者交換線上|

## <a name="information-on-the-attribute-behavior"></a>有關屬性行為的資訊
msExchangeUserHold 策略是單個許可權屬性。  可以在本地目錄中或雲目錄中的物件（本例中為使用者物件）上設置單個頒發屬性。  "頒發機構開始"規則規定，如果屬性從本地同步，則不允許 Azure AD 更新此屬性。

為了允許使用者在雲中的使用者物件上設置保留原則，使用雲MSExchangeUserHold策略屬性。 使用此屬性是因為 Azure AD 不能根據上述規則直接設置 msExchangeUserHold 策略。  如果 msExchangeUserHold 策略不為空，則此屬性將同步回本地目錄，並替換 msExchangeUserHold 策略的當前值。

例如，在某些情況下，如果同時在本地和 Azure 中更改了兩者，則可能會導致一些問題。  

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
