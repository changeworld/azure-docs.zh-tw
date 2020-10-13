---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563422"
---
使用 Azure 儲存體服務加密 (SSE) 將儲存在 Azure 檔案儲存體中的所有資料進行待用加密。 儲存體服務加密的運作方式與 Windows 上的 BitLocker 相似：資料會在檔案系統層級下加密。 由於資料是在 Azure 檔案共用的檔案系統下加密，因此當其編碼為磁碟時，不需要存取用戶端上的基礎金鑰，即可讀取或寫入 Azure 檔案共用。 待用加密同時適用於 SMB 和 NFS 通訊協定。

根據預設，儲存於 Azure 檔案儲存體中的資料是以使用 Microsoft 管理的金鑰加密。 使用 Microsoft 管理的金鑰時，Microsoft 會保存金鑰來加密/解密資料，並負責定期輪替。 您也可以選擇管理自己的金鑰，這可讓您控制輪替的程序。 如果您選擇使用客戶管理的金鑰來對檔案共用進行加密，則 Azure 檔案儲存體會獲授權存取您的金鑰，以滿足用戶端的讀取和寫入要求。 使用客戶管理的金鑰時，可以隨時撤銷此授權，但這表示您的 Azure 檔案共用將無法再透過 SMB 或 FileREST API 來進行存取。

Azure 檔案儲存體使用與其他 Azure 儲存體服務 (例如 Azure Blob 儲存體) 相同的加密配置。 如需深入了解 Azure 儲存體服務加密 (SSE) 的詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。