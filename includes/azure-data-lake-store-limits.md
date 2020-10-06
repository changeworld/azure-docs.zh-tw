---
title: 包含檔案
description: 包含檔案
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665585"
---
**Azure Data Lake Storage Gen2** 不是專用的服務或儲存體帳戶類型。 這是最新版本的功能，專門用於巨量資料分析。  這些功能適用於一般用途 v2 或 BlockBlobStorage 儲存體帳戶，您可藉由啟用帳戶的**階層命名空間**功能來加以取得。 如需調整目標，請參閱下列文章。 

- [調整 Blob 儲存體的目標](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage)。
- [調整標準儲存體帳戶的目標](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts)。

**Azure Data Lake Storage Gen1** 是專用的服務。 這是容納巨量資料分析工作負載的企業級超大規模存放庫。 您可以使用 Data Lake Storage Gen1 在單一位置擷取任何大小、類型和擷取速度的資料，以進行運作和探究分析。 您可以在 Data Lake Storage Gen1 帳戶中儲存的資料量並無任何限制。

| **Resource** | **限制** | **註解** |
| --- | --- | --- |
| 每個區域中每個訂用帳戶的 Data Lake Storage Gen1 帳戶數目上限 |10 | 若要要求提高此限制，請連絡支援人員。 |
| 每個檔案或資料夾的存取 ACL 數目上限 |32 | 這是一種硬性限制。 使用群組以較少的項目來管理存取。 |
| 每個檔案或資料夾的預設 ACL 數目上限 |32 | 這是一種硬性限制。 使用群組以較少的項目來管理存取。 |
