---
title: 啟用靜態加密-Azure 入口網站受控磁片
description: 使用 Azure 入口網站，為您的受控磁片資料啟用靜態加密。
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7fefaa3b13b73db119da696738af66e84f8bdd63
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136073"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure 入口網站-在受控磁片上啟用靜止的雙重加密

Azure 磁碟儲存體支援適用于受控磁片的雙重靜態加密。 如需有關靜態加密和其他受控磁片加密類型的概念資訊，請參閱磁片加密一文中的[雙重加密](disk-encryption.md#double-encryption-at-rest)待用一節。

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>後續步驟

- [Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [使用伺服器端加密來啟用客戶管理的金鑰-範例](disks-enable-customer-managed-keys-cli.md#examples)