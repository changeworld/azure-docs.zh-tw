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
ms.topic: conceptual
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396e1d9e6ad474d053ca803218d55396c073845d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680194"
---
# <a name="more-details-about-features-in-preview"></a>有關預覽中之功能的其他詳細資料
本主題描述如何使用預覽中目前的功能。

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect sync V2 端點 API (公開預覽) 

我們已為 Azure AD Connect 部署了新的端點 (API)，以提升 Azure Active Directory 同步處理服務作業的效能。 藉由使用新的 V2 端點，您將會在匯出和匯入至 Azure AD 時，經歷顯著的效能提升。 此新端點也支援同步具有最多 25 萬名成員的群組。 使用此端點也可供在啟用群組回寫時，將 O365 整合群組寫回至內部部署 Active Directory，而沒有成員資格上限。   如需詳細資訊，請參閱 [Azure AD Connect sync V2 端點 API (公開預覽)](how-to-connect-sync-endpoint-api-v2.md)。

## <a name="user-writeback"></a>使用者回寫
> [!IMPORTANT]
> 在 Azure AD Connect 的 2015 年 8 月更新中，已移除使用者的回寫預覽功能。 如果已啟用它，則您應該停用這個功能。
>
>

## <a name="next-steps"></a>後續步驟
繼續[自訂 Azure AD Connect 安裝](how-to-connect-install-custom.md)。

深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
