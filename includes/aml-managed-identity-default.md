---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147371"
---
**預設受控識別**是系統指派的受控識別，或第一個使用者指派的受控識別。

在執行期間，有兩個身分識別的應用程式：

1. 系統會使用身分識別來設定使用者的儲存體裝載、容器登錄和資料存放區。

    * 在此情況下，系統會使用預設管理的身分識別。

1. 使用者套用身分識別，以從程式碼記憶體取已提交執行的資源

    * 在此情況下，請提供對應至您要用來取得認證之受控識別的 *client_id* 。
    * 或者，透過 *DEFAULT_IDENTITY_CLIENT_ID* 環境變數取得使用者指派身分識別的用戶端識別碼。

    例如，若要使用預設受控識別來取得資料存放區的權杖：

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```