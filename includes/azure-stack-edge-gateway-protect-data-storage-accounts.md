---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900811"
---
您的裝置會與儲存體帳戶相關聯，以作為您在 Azure 中的資料目的地使用。 儲存體帳戶的存取權受到與該儲存體帳戶相關聯的訂用帳戶和兩個 512 位元的儲存體存取金鑰控制。

當 Data Box Edge 裝置存取儲存體帳戶時，其中一個金鑰會用於驗證。 系統會保留其他金鑰，讓您可以定期輪替金鑰。

基於安全性理由，許多資料中心需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 不要將密碼分發給其他使用者、進行硬式編碼，或將密碼儲存以純文字儲存在其他人可以存取的位置。
- 如果您認為帳戶金鑰可能遭到入侵，請透過 Azure 入口網站[重新產生帳戶金鑰](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys)。
- 您的 Azure 系統管理員應定期變更或重新產生主要或次要金鑰，方法是使用 Azure 入口網站的 [儲存體] 區段來直接存取儲存體帳戶。
