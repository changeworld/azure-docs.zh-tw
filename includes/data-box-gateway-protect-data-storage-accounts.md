---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581888"
---
您的裝置會與儲存體帳戶相關聯，以作為您在 Azure 中的資料目的地使用。 儲存體帳戶的存取權受到與該儲存體帳戶相關聯的訂用帳戶和兩個 512 位元的儲存體存取金鑰控制。

當 Azure Stack Edge 裝置存取儲存體帳戶時，會使用其中一個金鑰來進行驗證。 系統會保留其他金鑰，讓您可以定期輪替金鑰。

基於安全性理由，許多資料中心需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 請勿將密碼散發給其他使用者、進行硬式編碼，或將它儲存在其他人可存取的純文字中。
- 如果您認為帳戶金鑰可能遭到入侵，請透過 Azure 入口網站重新產生帳戶金鑰。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../articles/storage/common/storage-account-keys-manage.md)。
- 您的 Azure 系統管理員應定期變更或重新產生主要或次要金鑰，方法是使用 Azure 入口網站的 [儲存體] 區段來直接存取儲存體帳戶。