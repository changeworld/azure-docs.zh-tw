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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76118091"
---
## <a name="about-the-user-delegation-sas"></a>關於使用者委派 SAS

您可以使用 Azure AD 認證或帳戶金鑰來保護對容器或 blob 的存取 SAS 權杖。 使用 Azure AD 認證保護的 SAS 稱為使用者委派 SAS，因為用來簽署 SAS 的 OAuth 2.0 權杖是代表使用者要求的。

Microsoft 建議您盡可能使用 Azure AD 認證作為安全性最佳做法，而不是使用帳戶金鑰，這可能更容易遭到入侵。 當您的應用程式設計需要共用存取簽章時，請使用 Azure AD 認證來建立使用者委派 SAS 以獲得較佳的安全性。 如需使用者委派 SAS 的詳細資訊，請參閱 [建立使用者委派 sas](/rest/api/storageservices/create-user-delegation-sas)。

> [!CAUTION]
> 任何擁有有效 SAS 的用戶端都可以存取儲存體帳戶中的資料，如該 SAS 所允許。 保護 SAS 免于惡意或非預期的使用是很重要的。 在散發 SAS 時請謹慎使用，並備妥方案來撤銷遭盜用的 SAS。

如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](../articles/storage/common/storage-sas-overview.md)。
