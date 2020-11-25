---
title: 提高您開發的驗證和授權應用程式的復原能力
titleSuffix: Microsoft identity platform
description: 概述如何使用 Azure Active Directory 和 Microsoft 身分識別平臺來開發應用程式的復原能力指導方針
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: c2c2f9d0ad7bfa50f543b57326b9fc8dab0069c6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029297"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>提高您開發的驗證和授權應用程式的復原能力

Microsoft 身分識別會使用新式、權杖型驗證和授權。 這表示應用程式會從身分識別提供者取得權杖，以驗證使用者，以及授權應用程式呼叫受保護的 Api。

權杖的有效時間長度為特定時間長度，應用程式必須取得新的時間長度。 很少的情況下，取得權杖的呼叫可能會因為網路或基礎結構失敗或驗證服務中斷等問題而失敗。 在本檔中，我們將概述開發人員在權杖取得失敗時，可採取的步驟來提高應用程式的復原能力。

這些文章提供使用 Microsoft 身分識別平臺和 Azure Active Directory 來提高應用程式復原能力的指引。 有兩個用戶端應用程式的指導方針可代表已登入的使用者，以及代表自己運作的背景程式應用程式。 其中包含使用權杖和呼叫資源的最佳作法。

- [為登入使用者的應用程式建立恢復能力](resilience-client-app.md)
- [無需使用者即可在應用程式中建立彈性](resilience-daemon-app.md)
- [在您的身分識別與存取管理基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [使用 Azure Active Directory B2C 在您的客戶身分識別和存取管理中打造復原能力](resilience-b2c.md)
