---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157208"
---
儲存體帳戶的冗余選項包括：

* 本機多餘的儲存體（LRS）：簡單、低成本的冗余策略。 資料會在主要區域內以同步方式複製三次。
* 區域冗余儲存體（ZRS）：需要高可用性之案例的冗余。 資料會以同步方式複製到主要區域中的三個 Azure 可用性區域。
* 異地冗余存放裝置（GRS）：跨區域冗余以防止區域性中斷。 資料會在主要區域中同步複製三次，然後以非同步方式複製到次要區域。 如需次要區域中資料的讀取權限，請啟用讀取權限異地多餘儲存體（RA-GRS）。
* 異地區域-多餘儲存體（切換）（預覽）：需要高可用性和最大持久性的案例的冗余。 資料會在主要區域中的三個 Azure 可用性區域之間同步複製，然後以非同步方式複製到次要區域。 如需次要區域中資料的讀取權限，請啟用讀取權限異地區域-多餘儲存體（RA-切換）。

如需 Azure 儲存體中冗余選項的詳細資訊，請參閱[Azure 儲存體冗余](../articles/storage/common/storage-redundancy.md)。
