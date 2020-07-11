---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230953"
---
所有最新一代的 VM 大小都支援在主機上進行加密：

|類型  |不支援  |支援  |
|---------|---------|---------|
|一般用途     | Dv3、Dav4、Dv2、Av2        | B、DSv2、Dsv3、DC、DCv2、Dasv4        |
|計算最佳化     |         | Fsv2        |
|記憶體最佳化     | Ev3、Eav4        | DSv2、Esv3、M、Mv2、Easv4        |
|儲存體最佳化     |         | Ls，Lsv2 (NVMe 磁片未加密)         |
|GPU     | NC，NV        | NCv2、NCv3、ND、NVv3、NVv4、NDv2 (preview)         |
|高效能計算     | H        | HB、HC、HBv2        |
|上一代     | F、A、D、L、G        | DS、GS、Fs、NVv2        |

升級 VM 大小會導致驗證，以檢查新的 VM 大小是否支援 EncryptionAtHost 功能。
