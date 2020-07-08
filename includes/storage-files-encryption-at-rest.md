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
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597775"
---
Azure 檔案儲存體中儲存的所有資料都會使用 Azure 儲存體服務加密（SSE）在待用時加密。 儲存體服務加密的運作方式類似于 Windows 上的 BitLocker：資料會在檔案系統層級下加密。 因為資料會在 Azure 檔案共用的檔案系統底下加密，所以當其編碼為磁片時，您不需要存取用戶端上的基礎金鑰，即可讀取或寫入 Azure 檔案共用。

根據預設，儲存在 Azure 檔案儲存體中的資料會使用 Microsoft 管理的金鑰進行加密。 使用 Microsoft 管理的金鑰時，Microsoft 會保存金鑰來加密/解密資料，並負責定期輪替。 您也可以選擇管理您自己的金鑰，這可讓您控制輪替程式。 如果您選擇使用客戶管理的金鑰來加密您的檔案共用，Azure 檔案儲存體會獲授權存取您的金鑰，以滿足用戶端的讀取和寫入要求。 使用客戶管理的金鑰時，您可以隨時撤銷此授權，但這表示您的 Azure 檔案共用將無法再透過 SMB 或 FileREST API 來存取。

Azure 檔案儲存體使用與其他 Azure 儲存體服務（例如 Azure Blob 儲存體）相同的加密配置。 若要深入瞭解 Azure 儲存體服務加密（SSE），請參閱待用[資料的 Azure 儲存體加密](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。