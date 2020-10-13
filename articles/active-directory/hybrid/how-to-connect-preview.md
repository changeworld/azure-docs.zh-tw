---
title: Azure AD Connect：預覽版功能 | Microsoft Docs
description: 本主題詳細描述 Azure AD Connect 中位於預覽的功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b543cf4655d0b961a144e9180385a532ae4216d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657101"
---
# <a name="more-details-about-features-in-preview"></a>有關預覽中之功能的其他詳細資料
本主題描述如何使用預覽中目前的功能。

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect sync V2 端點 API (公開預覽) 

我們已為 Azure AD Connect 部署了新的端點 (API)，以提升 Azure Active Directory 同步處理服務作業的效能。 藉由使用新的 V2 端點，您將會在匯出和匯入至 Azure AD 時，經歷顯著的效能提升。 此新端點也支援同步具有最多 25 萬名成員的群組。 當群組回寫啟用時，使用此端點也可讓您將 Microsoft 365 的統一群組（沒有最大成員資格限制）回寫至內部部署 Active Directory。 如需詳細資訊，請參閱 [Azure AD Connect sync V2 端點 API (公開預覽)](how-to-connect-sync-endpoint-api-v2.md)。

## <a name="user-writeback"></a>使用者回寫
> [!IMPORTANT]
> 在 Azure AD Connect 的 2015 年 8 月更新中，已移除使用者的回寫預覽功能。 如果已啟用它，則您應該停用這個功能。
>
>

## <a name="next-steps"></a>後續步驟
繼續[自訂 Azure AD Connect 安裝](how-to-connect-install-custom.md)。

深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
