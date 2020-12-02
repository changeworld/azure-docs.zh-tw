---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466382"
---
您的裝置會與儲存體帳戶相關聯，以作為您在 Azure 中的資料目的地使用。 儲存體帳戶的存取權受到與該儲存體帳戶相關聯的訂用帳戶和兩個 512 位元的儲存體存取金鑰控制。

當 Data Box Edge 裝置存取儲存體帳戶時，其中一個金鑰會用於驗證。 系統會保留其他金鑰，讓您可以定期輪替金鑰。

基於安全性理由，許多資料中心需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 不要將密碼分發給其他使用者、進行硬式編碼，或將密碼儲存以純文字儲存在其他人可以存取的位置。
- 如果您認為帳戶金鑰可能遭到入侵，請透過 Azure 入口網站[重新產生帳戶金鑰](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)。
- 您的 Azure 系統管理員應定期變更或重新產生主要或次要金鑰，方法是使用 Azure 入口網站的 [儲存體] 區段來直接存取儲存體帳戶。