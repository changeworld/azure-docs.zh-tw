---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118091"
---
## <a name="about-the-user-delegation-sas"></a>關於使用者委派 SAS

可以使用 Azure AD 憑據或帳戶金鑰保護用於訪問容器或 Blob 的 SAS 權杖。 使用 Azure AD 憑據保護的 SAS 稱為使用者委派 SAS，因為用於對 SAS 進行簽名的 OAuth 2.0 權杖是代表使用者請求的。

Microsoft 建議您盡可能使用 Azure AD 憑據作為安全最佳做法，而不是使用帳戶金鑰，因為帳戶金鑰更易於洩露。 當應用程式設計需要共用訪問簽名時，請使用 Azure AD 憑據創建使用者委派 SAS 以獲得卓越的安全性。 有關使用者委派 SAS 的詳細資訊，請參閱[創建使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

> [!CAUTION]
> 任何擁有有效 SAS 的用戶端都可以在 SAS 允許的存儲帳戶中訪問資料。 保護 SAS 免受惡意或意外使用非常重要。 在分發 SAS 時使用自由裁量權，並制定計畫以撤銷受攻擊的 SAS。

有關共用訪問簽名的詳細資訊，請參閱[使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限](../articles/storage/common/storage-sas-overview.md)。
