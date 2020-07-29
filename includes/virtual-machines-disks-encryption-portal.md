---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177006"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

現在您已建立並設定您的金鑰保存庫和磁片加密集，您可以使用加密來部署 VM。
VM 部署程式類似于標準部署程式，唯一的差異在於您需要在與其他資源相同的區域中部署 VM，而且您選擇使用客戶管理的金鑰。

1. 搜尋**虛擬機器**，然後選取 [ **+ 新增**] 以建立 VM。
1. 在 [**基本**] 分頁上，選取與您的磁片加密集相同的區域，然後 Azure Key Vault]。
1. 視需要在 [**基本**] 分頁上填入其他值。

    ![VM 建立體驗的螢幕擷取畫面，並反白顯示區域值。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. 在 [**磁片**] 分頁上，選取 [待用**時使用客戶管理的金鑰進行加密**]。
1. 在 [**磁片加密集**] 下拉式選單中，選取您的磁片加密集。
1. 依您的需要進行其餘的選擇。

    ![VM 建立體驗的螢幕擷取畫面： [磁片] 分頁。 並反白顯示 [磁片加密集] 下拉式按鈕。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>在現有磁片上啟用

> [!CAUTION]
> 若要在任何連接至 VM 的磁片上啟用磁片加密，則需要停止 VM。
    
1. 流覽至與您其中一個磁片加密集位於相同區域中的 VM。
1. 開啟 VM，然後選取 [**停止**]。

    ![範例 VM 主要重迭的螢幕擷取畫面，其中反白顯示 [停止] 按鈕。](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM 完成停止之後，請選取 [**磁片**]，然後選取您想要加密的磁片。

    ![範例 VM 的螢幕擷取畫面，其中已開啟 [磁片] 分頁。 系統會反白顯示 OS 磁片，做為您要選取的範例磁片。](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. 選取 [**加密**]，然後選取 [**使用客戶管理的金鑰進行待用加密**]，然後在下拉式清單中選取您的磁片加密集。
1. 選取 [儲存]。

    ![範例作業系統磁片的螢幕擷取畫面。 [加密] 分頁已開啟，同時選取了 [使用客戶管理的金鑰進行待用加密]，以及您的範例 Azure Key Vault。 進行這些選擇之後，就會選取 [儲存] 按鈕。](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 針對連接至您想要加密之 VM 的任何其他磁片，重複此程式。
1. 當您的磁片完成切換到客戶管理的金鑰時，如果沒有任何其他您想要加密的連接磁片，您可以啟動 VM。
