---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895272"
---
Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於加密 blob 和檔案資料。

客戶管理的金鑰必須儲存在 Azure Key Vault 中。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。 如需 Azure 儲存體加密和金鑰管理的詳細資訊，請參閱[靜態資料的 Azure 儲存體加密](../articles/storage/common/storage-service-encryption.md)。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../articles/key-vault/key-vault-overview.md)
