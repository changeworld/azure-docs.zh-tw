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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597774"
---
Azure 檔提供兩個不同的存儲層（高級存儲層和標準層），允許您根據方案的性能和價格要求定制共用：

- **高級檔共用**：高級檔共用由固態磁碟機 （SSD） 支援，並部署在**檔存儲存儲帳戶**類型中。 對於大多數 IO 操作，高級檔共用在大多數 IO 操作中提供一致的高性能和低延遲，適用于 IO 密集型工作負載。 這使得它們適用于各種工作負載，如資料庫、網站託管和開發環境。 進階檔案共用僅適用於佈建計費模型。 有關高級檔共用的預配計費模型的詳細資訊，請參閱[瞭解高級檔共用的預配](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。
- **標準檔共用**：標準檔共用由硬碟磁碟機 （HDD） 支援，並部署在**通用版本 2 （GPv2） 存儲帳戶**類型中。 標準檔共用為對性能可變性不太敏感的 IO 工作負載（如通用檔共用和開發/測試環境）提供可靠的性能。 標準檔案共用僅適用於隨用隨付計費模型。