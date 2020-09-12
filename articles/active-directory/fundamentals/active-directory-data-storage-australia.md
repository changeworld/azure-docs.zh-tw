---
title: 適用于澳大利亞和紐西蘭客戶的身分識別資料存放區-Azure AD
description: 瞭解 Azure Active Directory 將其澳大利亞和紐西蘭客戶的身分識別相關資料儲存在哪裡。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7ed1d4c49c46673f1a1c2b5cb08b2467490acae
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565112"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory 中澳大利亞和紐西蘭客戶的身分識別資料儲存體

在訂閱 Microsoft 線上服務（例如 Microsoft 365 和 Azure）時，會根據組織提供的位址，在地理位置 Azure AD 儲存身分識別資料。 如需身分識別客戶資料儲存位置的相關資訊，您可以使用 Microsoft 信任中心的 [ [您的資料位於何處？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) ] 區段。

> [!NOTE]
> 與 Azure AD 整合的服務和應用程式可以存取身分識別的客戶資料。 評估您用來決定該特定服務和應用程式如何處理身分識別客戶資料的每個服務和應用程式，以及是否符合您公司的資料儲存需求。 若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 您的資料所在位置 區段。

針對在澳大利亞或紐西蘭提供位址的客戶，Azure AD 在澳大利亞資料中心內保存這些服務的身分識別資料： 
- Azure AD 目錄管理 
- 驗證

所有其他 Azure AD 服務都會將客戶資料儲存在全球資料中心。 若要找出服務的資料中心，請參閱 [Azure Active Directory –您的資料位於何處？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure MFA) 的多重要素驗證 (

MFA 會將身分識別客戶資料儲存在全球資料中心。 若要深入瞭解以雲端為基礎的 Azure MFA 和 Azure MFA 伺服器所收集和儲存的使用者資訊，請參閱 [Azure Multi-Factor Authentication 使用者資料收集](../authentication/concept-mfa-data-residency.md)。

## <a name="next-steps"></a>接下來的步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](../authentication/concept-mfa-howitworks.md)