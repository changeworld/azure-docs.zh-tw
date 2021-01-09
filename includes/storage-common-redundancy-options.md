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
ms.openlocfilehash: 65729934ea7c4037d6857aec10b14cdddd616368
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805593"
---
儲存體帳戶的備援選項包含：

* 本地備援儲存體 (LRS)：簡單、低成本的備援策略。 資料會在主要區域中的單一實體位置內同步複製三次。
* 區域備援儲存體 (ZRS)：需要高可用性案例的備援。 資料會同步複製到主要區域中的三個 Azure 可用性區域。
* 異地備援儲存體 (GRS)：跨區域備援，以防止區域性中斷。 資料會在主要區域中同步複製三次，然後以非同步方式複製到次要區域。 如需次要區域中資料的讀取存取權，請啟用讀取存取異地備援儲存體 (RA-GRS)。
* 地理區域備援儲存體 (GZRS) (預覽)：需要高可用性和最大持久性案例的備援。 資料會同步複製到主要區域中的三個 Azure 可用性區域，然後以非同步方式複製到次要區域。 如需次要區域中資料的讀取存取權，請啟用讀取存取異地區域備援儲存體 (RA-GZRS)。

如需 Azure 儲存體中備援選項的詳細資訊，請參閱 [Azure 儲存體備援](../articles/storage/common/storage-redundancy.md)。
