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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371766"
---
共用存取簽章（SAS）可讓您將有限的存取權授與儲存體帳戶中的容器和 blob。 當您建立 SAS 時，您會指定其條件約束，包括用戶端可存取的 Azure 儲存體資源、這些資源上的許可權，以及 SAS 有效的時間長度。

每個 SAS 都會以金鑰簽署。 您可以使用下列兩種方式的其中一種來簽署 SAS：

- 使用 Azure Active Directory （Azure AD）認證建立的金鑰。 以 Azure AD 認證簽署的 SAS 是*使用者委派*sas。
- 使用儲存體帳戶金鑰。 *服務 sas*和*帳戶 sas*都會使用儲存體帳戶金鑰進行簽署。

使用者委派 SAS 為使用儲存體帳戶金鑰簽署的 SAS 提供了絕佳的安全性。 Microsoft 建議盡可能使用使用者委派 SAS。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）授與資料的有限存取權](../articles/storage/common/storage-sas-overview.md)。
