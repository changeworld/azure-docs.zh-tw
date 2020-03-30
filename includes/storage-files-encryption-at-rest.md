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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597775"
---
存儲在 Azure 檔中的所有資料都使用 Azure 存儲服務加密 （SSE） 在靜態加密。 存儲服務加密的工作方式與 Windows 上的 BitLocker 類似：資料在檔案系統級別下加密。 由於資料在 Azure 檔共用的檔案系統下進行加密，因為它已編碼到磁片，因此不必訪問用戶端上的基礎金鑰來讀取或寫入 Azure 檔共用。

預設情況下，存儲在 Azure 檔中的資料使用 Microsoft 管理的金鑰進行加密。 使用 Microsoft 管理的金鑰，Microsoft 擁有加密/解密資料的金鑰，並負責定期輪換資料。 您還可以選擇管理自己的金鑰，從而控制旋轉過程。 如果選擇使用客戶管理的金鑰加密檔共用，Azure 檔將有權訪問金鑰以完成來自用戶端的讀取和寫入請求。 使用客戶管理的金鑰，您可以隨時撤銷此授權，但這意味著 Azure 檔共用將不再通過 SMB 或 FileREST API 訪問。

Azure 檔使用與其他 Azure 存儲服務（如 Azure Blob 存儲）相同的加密方案。 要瞭解有關 Azure 存儲服務加密 （SSE） 的更多，請參閱[Azure 存儲加密以瞭解靜態資料](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。