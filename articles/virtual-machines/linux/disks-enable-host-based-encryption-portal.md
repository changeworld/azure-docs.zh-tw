---
title: 使用在主機 Azure 入口網站受控磁片上的加密來啟用端對端加密
description: 在主機上使用加密來啟用 Azure 受控磁片上的端對端加密-Azure 入口網站。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171524"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>在主機上使用加密來啟用端對端加密-Azure 入口網站

當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 如需在主機上進行加密的概念資訊，以及其他受控磁片加密類型，請參閱[VM 資料的主機端對端](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)加密加密。

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>後續步驟

[Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)