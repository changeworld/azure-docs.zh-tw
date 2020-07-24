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
ms.openlocfilehash: 2d4ab180818e597afa86a4cfdf73c6948d44e630
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132446"
---
如果您是第一次執行，則為您的磁片設定客戶管理的金鑰將會要求您以特定順序建立資源。 首先，您將需要建立並設定 Azure Key Vault。

## <a name="set-up-your-azure-key-vault"></a>設定 Azure 金鑰保存庫

1. 登入 [Azure 入口網站](https://aka.ms/diskencryptionupdates)。
1. 搜尋並選取 [**金鑰保存庫**]。

    [![server-side-encryption-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > 您的 Azure 金鑰保存庫、磁片加密集、VM、磁片和快照必須全都位於相同的區域和訂用帳戶，部署才會成功。

1. 選取 [ **+ 新增**] 以建立新的 Key Vault。
1. 建立新的資源群組。
1. 輸入金鑰保存庫名稱、選取區域，然後選取 [定價層]。

    > [!NOTE]
    > 建立 Key Vault 執行個體時，您必須啟用虛刪除和清除保護。 虛刪除可確保 Key Vault 將已刪除的金鑰保留一段指定的保留期間 (預設為 90 天)。 清除保護可確保已刪除的金鑰在保留期限結束之前，無法永久刪除。 這些設定可防止您因為意外刪除而遺失資料。 使用 Key Vault 來加密受控磁碟時，這些設定是必要的。

1. 選取 [審核] [ **+ 建立**]，驗證您的選擇，然後選取 [**建立**]。

    ![Azure Key Vault 建立體驗的螢幕擷取畫面。 顯示您所建立的特定值](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. 完成部署金鑰保存庫後，請選取它。
1. 選取 [**設定**] 底下的 [**金鑰**]。
1. 選取 [產生/匯入]。

    ![[Key Vault 資源設定] 窗格的螢幕擷取畫面。 顯示 [設定] 內的 [產生/匯入] 按鈕。](media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. 將 [**金鑰類型**] 設定為 [ **rsa** ]，並將 [ **rsa 金鑰大小**] 設定為**2048**。
1. 視需要填寫剩餘的選項，然後選取 [**建立**]。

    ![選取 [產生/匯入] 按鈕後出現的 [建立金鑰] 分頁的螢幕擷取畫面](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>設定磁片加密集

1. 搜尋**磁片加密集**並加以選取。
1. 在 [**磁片加密設定**] 分頁上，選取 [ **+ 新增**]。

    ![磁片加密入口網站主畫面的螢幕擷取畫面。 反白顯示 [新增] 按鈕](media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. 選取您的資源群組、命名您的加密集，然後選取與金鑰保存庫相同的區域。
1. 針對 [**加密類型**]，選取 [待用待用**加密] 與客戶管理的金鑰**。

    > [!NOTE]
    > 建立具有特定加密類型的磁片加密集之後，就無法變更它。 如果您想要使用不同的加密類型，您必須建立新的磁片加密集。

1. 選取 [**按一下以選取索引鍵**]。
1. 選取您先前建立的金鑰保存庫和金鑰，以及版本。
1. 按 [選取]****。
1. 選取 [審核] [ **+ 建立**]，然後按一下 [**建立**]。

    ![[磁片加密建立] 分頁的螢幕擷取畫面。 顯示訂用帳戶、資源群組、磁片加密集名稱、區域，以及金鑰保存庫 + 金鑰選取器。](media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. 完成建立後，請開啟磁片加密集，然後選取要顯示的警示。

    ![警示快顯視窗的螢幕擷取畫面： ' 若要將磁片、映射或快照集與磁片加密組產生關聯，您必須將許可權授與金鑰保存庫 '。 選取此警示以繼續](media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    兩個通知應該會快顯並成功。 這可讓您使用金鑰保存庫的磁片加密集。

    ![金鑰保存庫的成功許可權和角色指派的螢幕擷取畫面。](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)