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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895272"
---
Azure 存儲在靜態存儲帳戶中加密所有資料。 預設情況下，資料使用 Microsoft 管理的金鑰進行加密。 為了對加密金鑰進行其他控制，可以提供客戶管理的金鑰，用於加密 Blob 和檔資料。

客戶管理的金鑰必須存儲在 Azure 金鑰保存庫中。 您可以創建自己的金鑰並將其存儲在金鑰保存庫中，也可以使用 Azure 金鑰保存庫 API 生成金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。 有關 Azure 存儲加密和金鑰管理的詳細資訊，請參閱[靜態資料的 Azure 存儲加密](../articles/storage/common/storage-service-encryption.md)。 有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](../articles/key-vault/key-vault-overview.md)
