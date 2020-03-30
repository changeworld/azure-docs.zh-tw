---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468672"
---
您的設備與用作 Azure 中資料目標的存儲帳戶相關聯。 對存儲帳戶的訪問由訂閱和與該存儲帳戶關聯的兩個 512 位存儲訪問金鑰控制。

當資料框邊緣設備訪問存儲帳戶時，其中一個金鑰用於身份驗證。 另一個鍵保留在保留中，因此您可以定期旋轉鍵。

出於安全原因，許多資料中心需要金鑰輪換。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 小心保護您的帳戶金鑰。 不要將密碼分發給其他使用者，不要對其進行硬編碼，或將其保存在其他人可訪問的純文字中的任何位置。
- 如果您認為帳戶金鑰可能受到威脅，則通過 Azure 門戶重新生成帳戶金鑰。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../articles/storage/common/storage-account-keys-manage.md)。
- Azure 管理員應通過使用 Azure 門戶的"存儲"部分直接存取存儲帳戶，定期更改或重新生成主金鑰或輔助金鑰。