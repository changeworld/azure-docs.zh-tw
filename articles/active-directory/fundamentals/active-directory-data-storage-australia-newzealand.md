---
title: 適用于澳大利亞客戶的身分識別資料儲存-Azure AD
description: 瞭解 Azure Active Directory 在何處儲存其澳大利亞客戶的身分識別相關資料。
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
ms.openlocfilehash: 494b949594574a55df405ec0447fa6c59f386f7d
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799032"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory 中澳大利亞和紐西蘭客戶的客戶資料儲存體

Azure Active Directory (Azure AD) 會根據您註冊 Microsoft 線上服務時所提供的國家/地區，將其客戶資料儲存在地理位置。 Microsoft Online services 包含 Office 365 和 Azure。 

如需 Azure AD 和其他 Microsoft 服務資料所在位置的相關資訊，請參閱 Microsoft 信任中心的[您的資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)一節。

自2020年2月26日起，Microsoft 開始在澳大利亞的資料中心內，使用澳大利亞或紐西蘭的帳單位址，為新的租使用者儲存 Azure AD 的客戶資料。 Microsoft 會在2020年5月1日到2020年10月31日前，將具有澳大利亞或紐西蘭帳單位址的現有租使用者遷移至澳大利亞資料中心，而不需要任何客戶動作。 遷移程式不會牽涉到客戶任何停機時間，也不會影響租使用者在遷移期間的任何功能。

此外，某些 Azure AD 功能尚未支援澳大利亞的客戶資料儲存。 如需特定功能資訊，請移至[Azure AD 資料地圖](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)。 例如，Microsoft Azure 多重要素驗證會將客戶資料儲存在美國，並全域處理。 [如需 Azure 多重要素驗證，請參閱資料常駐和客戶資料](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)。

> [!NOTE]
> 與 Azure AD 整合的 Microsoft 產品、服務和協力廠商應用程式可以存取客戶資料。 評估您使用的每個產品、服務和應用程式，以判斷該特定產品、服務和應用程式如何處理客戶資料，以及是否符合您公司的資料儲存需求。 若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 [您的資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 區段。

