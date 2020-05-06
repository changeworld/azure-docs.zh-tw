---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562114"
---
您的裝置會與儲存體帳戶相關聯，以作為您在 Azure 中的資料目的地使用。 儲存體帳戶的存取權是由訂用帳戶和與該儲存體帳戶相關聯的 2 512 位儲存體存取金鑰所控制。

當 Azure Stack Edge 裝置存取儲存體帳戶時，會使用其中一個金鑰來進行驗證。 另一個金鑰是保留的，因此您可以定期輪替金鑰。

基於安全性理由，許多資料中心都需要金鑰輪替。 建議您按照這些最佳作法進行金鑰輪替：

- 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 謹慎保護您的帳戶金鑰。 不要將密碼散發給其他使用者、硬式編碼，或將它儲存在其他人可以存取的純文字位置。
- 如果您認為您的帳戶金鑰可能遭到入侵，請透過 Azure 入口網站重新產生。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../articles/storage/common/storage-account-keys-manage.md)。
- 您的 Azure 系統管理員應該使用 Azure 入口網站的 [儲存體] 區段來定期變更或重新產生主要或次要金鑰，以直接存取儲存體帳戶。