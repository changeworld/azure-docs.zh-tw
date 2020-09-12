---
title: 適用于澳大利亞和紐西蘭客戶的客戶資料存放區-Azure AD
description: 瞭解 Azure Active Directory 為其澳大利亞和紐西蘭客戶儲存客戶相關資料的位置。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bbd31bf47c7ce7ae75a6934da0ea769698ef187
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565101"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>適用于澳大利亞和紐西蘭客戶的客戶資料儲存體 Azure Active Directory

Azure Active Directory (Azure AD) 會根據您註冊 Microsoft Online service 時所提供的國家/地區，將其客戶資料儲存在地理位置。 Microsoft Online services 包含 Microsoft 365 和 Azure。 

如需 Azure AD 和其他 Microsoft 服務的資料所在位置的相關資訊，請參閱 Microsoft 信任中心的「 [您的資料位於何處？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 」一節。

自2020年2月26日起，Microsoft 開始將新租使用者的客戶資料 Azure AD 儲存在澳大利亞資料中心內的新租使用者，以及澳大利亞或紐西蘭帳單位址。 在2020年5月1日到2020年10月31日之間，Microsoft 會將擁有澳大利亞或紐西蘭帳單位址的現有租使用者遷移至澳大利亞資料中心，而不需要任何客戶動作。 遷移程式不會涉及任何客戶的停機時間，也不會影響任何租使用者在遷移期間的功能。

此外，某些 Azure AD 功能尚不支援在澳大利亞儲存客戶資料。 請移至 [Azure AD 資料對應](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)，以取得特定的功能資訊。 例如，Microsoft Azure Multi-Factor Authentication 會將客戶資料儲存在美國，並在全球處理。 請參閱 [Azure Multi-Factor Authentication 的資料存放區和客戶資料](../authentication/concept-mfa-data-residency.md)。

> [!NOTE]
> 與 Azure AD 整合的 Microsoft 產品、服務和協力廠商應用程式可以存取客戶資料。 評估您用來決定該特定產品、服務和應用程式如何處理客戶資料的每個產品、服務和應用程式，以及是否符合您公司的資料儲存需求。 若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 [您的資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 區段。