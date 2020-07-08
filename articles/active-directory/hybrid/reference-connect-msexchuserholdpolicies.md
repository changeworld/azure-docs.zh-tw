---
title: Azure AD Connect： msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies |Microsoft Docs
description: 本主題描述 msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies 屬性的屬性行為
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74213082"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies
下列參考檔說明 Exchange 使用的這些屬性，以及編輯預設同步處理規則的適當方式。

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>什麼是 msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies？
有兩種類型的[保留](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019)可用於 Exchange 伺服器：訴訟保留和就地保存。 啟用「訴訟保存」時，所有的信箱都會保留所有專案。  就地保存是用來只保留符合您使用就地電子檔探索工具定義之搜尋查詢準則的專案。

MsExchUserHoldPolcies 和 cloudMsExchUserHoldPolicies 屬性可讓內部部署 AD 和 Azure AD 根據使用內部部署 Exchange 或 Exchange 線上的情況，判斷哪些使用者正在進行保存。

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies 同步處理流程
根據預設，MsExchUserHoldPolcies 會藉由直接 Azure AD Connect 同步處理至元 # 中的 msExchUserHoldPolicies 屬性，然後在中進行 msExchUserHoldPolices 屬性的 Azure AD

下表說明流程：

從內部部署 Active Directory 的輸入：

|Active Directory 屬性|屬性名稱|流程類型|元屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|內部部署 Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolices|In from AD-使用者交換|

輸出至 Azure AD：

|元屬性|屬性名稱|流程類型|Azure AD 屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies 同步處理流程
根據預設，cloudMsExchUserHoldPolicies 會直接 Azure AD Connect 同步處理至元處理中的 cloudMsExchUserHoldPolicies 屬性。 然後，如果 msExchUserHoldPolices 在元 # 中不是 null，則中的屬性會流動以 Active Directory。

下表說明流程：

從 Azure AD 輸入：

|Active Directory 屬性|屬性名稱|流程類型|元屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|內部部署 Active Directory|cloudMsExchUserHoldPolicies|直接|cloudMsExchUserHoldPolicies|In from AAD-使用者交換|

輸出至內部部署 Active Directory：

|元屬性|屬性名稱|流程類型|Azure AD 屬性|同步處理規則|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF （NOT Null）|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>屬性行為的資訊
MsExchangeUserHoldPolicies 是單一授權屬性。  可以在內部部署目錄或雲端目錄中的物件（在此案例中為 user 物件）設定單一授權屬性。  授權的啟動規則會指示，如果屬性是從內部部署同步處理，則不允許 Azure AD 更新此屬性。

若要允許使用者在雲端中的使用者物件上設定保存原則，請使用 cloudMSExchangeUserHoldPolicies 屬性。 因為 Azure AD 無法直接根據上述規則來設定 msExchangeUserHoldPolicies，所以會使用這個屬性。  這個屬性接著會同步處理回內部部署目錄（如果是）、msExchangeUserHoldPolicies 不是 null，並取代目前的 msExchangeUserHoldPolicies 值。

例如，在某些情況下，如果兩者同時在內部部署和 Azure 中變更，這可能會造成一些問題。  

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
