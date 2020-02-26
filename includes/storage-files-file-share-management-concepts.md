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
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597778"
---
Azure 檔案共用會部署到*儲存體帳戶*，這是代表共用儲存體集區的最上層物件。 此儲存體集區可用來部署多個檔案共用，以及其他儲存體資源（例如 blob 容器、佇列或資料表）。 部署到儲存體帳戶的所有儲存體資源都會共用適用于該儲存體帳戶的限制。 若要查看儲存體帳戶目前的限制，請參閱[Azure 檔案儲存體擴充性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

有兩種主要類型的儲存體帳戶，您將用於 Azure 檔案儲存體部署： 
- **一般目的版本2（GPv2）儲存體帳戶**： GPv2 儲存體帳戶可讓您在以標準/硬碟為基礎（HDD）的硬體上部署 Azure 檔案共用。 除了儲存 Azure 檔案共用，GPv2 儲存體帳戶還可以儲存其他儲存體資源，例如 blob 容器、佇列或資料表。 
- **FileStorage 儲存體帳戶**： FileStorage 儲存體帳戶可讓您在 premium/固態磁片型（SSD 型）硬體上部署 Azure 檔案共用。 FileStorage 帳戶只能用來儲存 Azure 檔案共用;不能在 FileStorage 帳戶中部署其他儲存體資源（blob 容器、佇列、資料表等等）。

在 Azure 入口網站、PowerShell 或 CLI 中，您可能會遇到數個其他的儲存體帳戶類型。 兩個儲存體帳戶類型 BlockBlobStorage 和 BlobStorage 儲存體帳戶不能包含 Azure 檔案共用。 您可能會看到的其他兩個儲存體帳戶類型為一般用途第1版（GPv1）和傳統儲存體帳戶，這兩者都可以包含 Azure 檔案共用。 雖然 GPv1 和傳統儲存體帳戶可能包含 Azure 檔案共用，但 Azure 檔案儲存體的大部分新功能僅適用于 GPv2 和 FileStorage 儲存體帳戶。 因此，我們建議僅針對新的部署使用 GPv2 和 FileStorage 儲存體帳戶，並在您的環境中已存在時，升級 GPv1 和傳統儲存體帳戶。  