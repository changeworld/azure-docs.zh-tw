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
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597774"
---
Azure 檔案儲存體提供兩個不同層級的儲存體（premium 和 standard），可讓您根據案例的效能和價格需求，量身打造您的共用：

- **Premium 檔案共用**：高階檔案共用是由固態硬碟（ssd）所支援，並部署在**FileStorage 儲存體帳戶**類型中。 高階檔案共用可針對 IO 密集型工作負載，在大部分 IO 作業的單一位數毫秒內，提供一致的高效能和低延遲。 這讓它們適用于各種不同的工作負載，例如資料庫、網站裝載和開發環境。 進階檔案共用僅適用於佈建計費模型。 如需有關 premium 檔案共用布建計費模型的詳細資訊，請參閱[瞭解 premium 檔案共用](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)的布建。
- **標準檔案共用**：標準檔案共用是由硬碟（hdd）所支援，且會部署在**一般目的版本2（GPv2）儲存體帳戶**類型中。 標準檔案共用可針對效能變化較不敏感的 IO 工作負載提供可靠的效能，例如一般用途的檔案共用和開發/測試環境。 標準檔案共用僅適用於隨用隨付計費模型。