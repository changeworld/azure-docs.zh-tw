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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371766"
---
共用訪問簽名 （SAS） 使您能夠授予對存儲帳戶中的容器和 Blob 的有限存取權限。 創建 SAS 時，可以指定其約束，包括允許用戶端存取哪些 Azure 存儲資源、它們對這些資源具有哪些許可權以及 SAS 的有效期。

每個 SAS 都使用金鑰進行簽名。 您可以通過兩種方式之一對 SAS 進行簽名：

- 使用使用 Azure 活動目錄 （Azure AD） 憑據創建的金鑰。 使用 Azure AD 憑據簽名的 SAS 是*使用者委派*SAS。
- 使用存儲帳戶金鑰。 服務*SAS*和*帳戶 SAS*都使用存儲帳戶金鑰進行簽名。

使用者委派 SAS 為使用存儲帳戶金鑰簽名的 SAS 提供了卓越的安全性。 Microsoft 建議盡可能使用使用者委派 SAS。 有關詳細資訊，請參閱[授予對具有共用訪問簽名 （SAS） 的資料的有限存取權限](../articles/storage/common/storage-sas-overview.md)。
