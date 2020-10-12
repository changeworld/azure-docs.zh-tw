---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648798"
---
Azure 儲存體會加密待用儲存體帳戶中的所有資料。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../articles/storage/common/storage-service-encryption.md)。

根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於加密 blob 和檔案資料。 這些金鑰必須存在於 Azure Key Vault 中，Functions 才能存取儲存體帳戶。 若要深入了解，請參閱[使用客戶管理的金鑰待用加密](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md)。  