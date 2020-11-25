---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011689"
---
Azure 檔案共用已部署到 *儲存體帳戶*，這是代表共用儲存體集區的最上層物件。 此儲存體集區可用來部署多個檔案共用，以及其他儲存體資源 (例如 Blob 容器、佇列或資料表)。 部署到儲存體帳戶的所有儲存體資源，都共用適用於該儲存體帳戶的限制。 若要查看儲存體帳戶的目前限制，請參閱 [Azure 檔案儲存體的可擴縮性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

要用於 Azure 檔案儲存體部署的儲存體帳戶有兩種主要類型： 
- **一般用途第 2 版 (GPv2) 儲存體帳戶**：GPv2 儲存體帳戶可讓您在標準/硬碟型 (HDD 型) 的硬體上部署 Azure 檔案共用。 除了儲存 Azure 檔案共用，GPv2 儲存體帳戶還可以儲存其他儲存體資源，例如 Blob 容器、佇列或資料表。 
- **FileStorage 儲存體帳戶**：FileStorage 儲存體帳戶可讓您在進階/固態磁碟型 (SSD 型) 硬體上部署 Azure 檔案共用。 FileStorage 帳戶只能用來儲存 Azure 檔案共用；不能在 FileStorage 帳戶中部署其他儲存體資源 (Blob 容器、佇列、資料表等等)。 只有 FileStorage 帳戶可以部署 SMB 和 NFS 檔案共用。

在 Azure 入口網站、PowerShell 或 CLI 中，可能會遇到數個其他儲存體帳戶類型。 BlockBlobStorage 和 BlobStorage 這兩個儲存體帳戶類型，不能包含 Azure 檔案共用。 您可能會看到其他兩個儲存體帳戶類型為一般用途第 1 版 (GPv1) 和傳統儲存體帳戶，這兩者都可以包含 Azure 檔案共用。 雖然 GPv1 和傳統儲存體帳戶可能包含 Azure 檔案共用，但 Azure 檔案儲存體的大部分新功能僅適用於 GPv2 和 FileStorage 儲存體帳戶。 因此，我們建議僅將 GPv2 和 FileStorage 儲存體帳戶用於新的部署，若環境中已存在 GPv1 和傳統儲存體帳戶時，則建議對其進行升級。  