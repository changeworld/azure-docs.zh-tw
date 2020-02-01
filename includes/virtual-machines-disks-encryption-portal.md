---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0912316d1c41f46e5dba74b58017f4fd5e8ed529
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909125"
---
### <a name="portal"></a>入口網站

如果您是第一次執行，則為您的磁片設定客戶管理的金鑰將會要求您以特定順序建立資源。 首先，您將需要建立並設定 Azure Key Vault。

#### <a name="setting-up-your-azure-key-vault"></a>設定您的 Azure Key Vault

1. 登入[Azure 入口網站](https://portal.azure.com/)並搜尋 Key Vault
1. 搜尋並選取 [**金鑰保存庫**]。

    [![sse-key-vault-portal-search .png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > 您的 Azure 金鑰保存庫、磁片加密集、VM、磁片和快照必須全都位於相同的區域和訂用帳戶，部署才會成功。

1. 選取 [ **+ 新增**] 以建立新的 Key Vault。
1. 建立新的資源群組
1. 輸入金鑰保存庫名稱、選取區域，然後選取 [定價層]。
1. 選取 [審核] [ **+ 建立**]，驗證您的選擇，然後選取 [**建立**]。

    ![sse-create-a-key-vault .png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 完成部署金鑰保存庫後，請選取它。
1. 選取 [**設定**] 底下的 [**金鑰**]。
1. 選取**產生/匯入**

    ![sse-key-vault-generate-settings .png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 將 [**金鑰類型**] 設定為 [ **rsa** ]，並將 [ **rsa 金鑰大小**] 設定為**2080**。
1. 視需要填寫剩餘的選項，然後選取 [**建立**]。

    ![sse-create-a-key-generate .png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>設定磁片加密集

若要建立及設定磁片加密集，您必須使用下列連結： https://aka.ms/diskencryptionsets 。 全域 Azure 入口網站中尚未提供磁片加密集建立。

1. 開啟 [[磁片加密集] 連結](https://aka.ms/diskencryptionsets)。
1. 選取 [+新增]。

    ![sse-create-disk-encryption-set .png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 選取您的資源群組、命名您的加密集，然後選取與金鑰保存庫相同的區域。
1. 選取 [**金鑰保存庫] 和 [金鑰**]。
1. 選取您先前建立的金鑰保存庫和金鑰，以及版本。
1. 按 [選取]。
1. 選取 [審核] [ **+ 建立**]，然後按一下 [**建立**]。

    ![sse-disk-enc-set-blade-key .png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成建立後，請開啟磁片加密集，然後選取要顯示的警示。

    ![sse-disk-enc-alert-fix .png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

兩個通知應該會快顯並成功。 這麼做可讓您使用金鑰保存庫中的磁片加密集。

![disk-enc-notification-success .png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

現在您已建立並設定您的金鑰保存庫和磁片加密集，您可以使用加密來部署 VM。
VM 部署程式類似于標準部署程式，唯一的差異在於您需要在與其他資源相同的區域中部署 VM，而且您選擇使用客戶管理的金鑰。

1. 開啟 [[磁片加密集] 連結](https://aka.ms/diskencryptionsets)。
1. 搜尋**虛擬機器**，然後選取 [ **+ 新增**] 以建立 VM。
1. 在 **基本** 索引標籤上，選取與您的磁片加密集相同的區域，然後 Azure Key Vault。
1. 視需要在 [**基本**] 索引標籤上填入其他值。

    ![sse-create-a-vm-region .png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在 [**磁片**] 索引標籤上，選取 [待用**時使用客戶管理的金鑰進行加密**]。
1. 在 [**磁片加密集**] 下拉式選單中，選取您的磁片加密集。
1. 依您的需要進行其餘的選擇。

    ![sse-create-vm-select-cmk-encryption-set .png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>在現有磁片上啟用

若要在現有磁片上管理和設定磁片加密，您必須使用下列連結： https://aka.ms/diskencryptionsets 。 在現有磁片上啟用客戶管理的金鑰尚無法在全域 Azure 入口網站中使用。

> [!CAUTION]
> 若要在任何連接至 VM 的磁片上啟用磁片加密，則需要停止 VM。

1. 開啟 [[磁片加密集] 連結](https://aka.ms/diskencryptionsets)。
1. 流覽至與您的其中一個磁片加密集位於相同區域的 VM。
1. 開啟 VM，然後選取 [**停止**]。

    ![sse-stop-VM-to-encrypt-disk .png](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. VM 完成停止之後，請選取 [**磁片**]，然後選取您想要加密的磁片。

    ![sse-existing-disk-select .png](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. 選取 [**加密**]，然後選取 [**使用客戶管理的金鑰進行待用加密**]，然後在下拉式清單中選取您的磁片加密集。
1. 選取 [儲存]。

    ![sse-encrypt-existing-disk-customer-managed-key .png](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 針對連接至您想要加密之 VM 的任何其他磁片，重複此程式。
1. 當您的磁片完成切換到客戶管理的金鑰時，如果沒有任何其他您想要加密的連接磁片，您可以啟動 VM。
