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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597778"
---
Azure 檔共用部署到*存儲帳戶*中，這些帳戶是表示共用存儲池的頂級物件。 此存儲池可用於部署多個檔共用以及其他存儲資源，如 Blob 容器、佇列或表。 部署到存儲帳戶中的所有存儲資源都共用適用于該存儲帳戶的限制。 要查看存儲帳戶的當前限制，請參閱 Azure[檔可伸縮性和性能目標](../articles/storage/files/storage-files-scale-targets.md)。

有兩種主要類型的存儲帳戶將用於 Azure 檔部署： 
- **通用版本 2 （GPv2） 存儲帳戶**：GPv2 存儲帳戶允許您在基於標準/基於硬碟（基於 HDD）的硬體上部署 Azure 檔共用。 除了存儲 Azure 檔共用外，GPv2 存儲帳戶還可以存儲其他存儲資源，如 Blob 容器、佇列或表。 
- **檔存儲存儲帳戶**：檔存儲存儲帳戶允許您在基於高級/固態磁片（基於 SSD）的硬體上部署 Azure 檔共用。 檔存儲帳戶只能用於存儲 Azure 檔共用;因此，檔存儲帳戶只能用於存儲 Azure 檔共用。無法在 FileStorage 帳戶中部署其他存儲資源（blob 容器、佇列、表等）。

在 Azure 門戶、PowerShell 或 CLI 中可能會遇到幾種其他存儲帳戶類型。 兩種存儲帳戶類型（塊 Blob 存儲帳戶和 Blob 存儲存儲帳戶）不能包含 Azure 檔共用。 您可能會看到的其他兩種存儲帳戶類型是通用版本 1 （GPv1） 和經典存儲帳戶，這兩種帳戶都可以包含 Azure 檔共用。 儘管 GPv1 和經典存儲帳戶可能包含 Azure 檔共用，但 Azure 檔的大多數新功能僅在 GPv2 和檔存儲存儲帳戶中可用。 因此，我們建議僅將 GPv2 和 FileStorage 存儲帳戶用於新部署，並在環境中已存在 GPv1 和經典存儲帳戶時升級這些帳戶。  