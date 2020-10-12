---
title: 使用 Azure Resource Manager 範本建立和加密虛擬機器擴展集
description: 在本快速入門中，您將了解如何使用 Azure Resource Manager 範本來建立和加密虛擬機器擴展集
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4284e94f8d8d0effd160c5048f54fcbede417e38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129761"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>使用 Azure Resource Manager 加密虛擬機器擴展集

使用 Azure Resource Manager 範本將 Linux 虛擬機器擴展集加密或解密。

## <a name="deploying-templates"></a>部署範本

首先，請選取符合您案例的範本。

- [在執行中的 Linux 虛擬機器擴展集上啟用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [在執行中的 Windows 虛擬機器擴展集上啟用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [使用 Jumpbox 部署 Linux VM 的虛擬機器擴展集並在 Linux 虛擬機器擴展集上啟用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [使用 Jumpbox 部署 Windows VM 的虛擬機器擴展集並在 Windows 虛擬機器擴展集上啟用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [在執行中的 Linux 虛擬機器擴展集上停用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [在執行中的 Windows 虛擬機器擴展集上停用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

接著，遵循下列步驟：

1. 按一下 [ **部署至 Azure**]。
2. 填寫必要欄位，然後同意條款及條件。
3. 按一下 [購買]**** 以部署範本。

## <a name="next-steps"></a>接下來的步驟

- [適用於虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)
- [使用 Azure CLI 將虛擬機器擴展集加密](disk-encryption-cli.md)
- [使用 Azure PowerShell 將虛擬機器擴展集加密](disk-encryption-powershell.md)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)
- [使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密](disk-encryption-extension-sequencing.md)
