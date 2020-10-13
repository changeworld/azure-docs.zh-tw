---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4170671b1dbcb8d2932b54f101cf5691b2461e31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86035937"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>從 Azure 入口網站複製您的認證

範例應用程式需要驗證對您儲存體帳戶的存取。 若要進行驗證，請以連接字串的形式將儲存體帳戶認證新增至應用程式。 請依照下列步驟檢視您的儲存體帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在儲存體帳戶概觀的 [設定]  區段中，選取 [存取金鑰]  。 在此處，您可以檢視帳戶存取金鑰，和每個金鑰的完整連接字串。
4. 尋找 [金鑰 1] 下方的 [連接字串] 值，然後選取 [複製] 按鈕以複製連接字串。 在下一個步驟中，您會將連接字串值新增至環境變數。

    ![顯示如何從 Azure 入口網站複製連接字串的螢幕擷取畫面](media/storage-copy-connection-string-portal/portal-connection-string.png)
