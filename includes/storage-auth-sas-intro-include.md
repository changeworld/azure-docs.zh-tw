---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75371766"
---
 (SAS) 的共用存取簽章可讓您對儲存體帳戶中的容器和 blob 授與有限的存取權。 當您建立 SAS 時，您會指定其條件約束，包括用戶端允許存取哪些 Azure 儲存體資源、這些資源的許可權，以及 SAS 的有效期限。

每個 SAS 都會以金鑰簽署。 您可以使用下列兩種方式之一來簽署 SAS：

- 使用 Azure Active Directory (Azure AD) 認證來建立金鑰。 使用 Azure AD 認證簽署的 SAS 是 *使用者委派* sas。
- 使用儲存體帳戶金鑰。 *服務 sas*和*帳戶 sas*都會使用儲存體帳戶金鑰進行簽署。

使用者委派 SAS 可為使用儲存體帳戶金鑰簽署的 SAS 提供絕佳的安全性。 Microsoft 建議盡可能使用使用者委派 SAS。 如需詳細資訊，請參閱 [使用共用存取簽章授與對資料的有限存取 (SAS) ](../articles/storage/common/storage-sas-overview.md)。
