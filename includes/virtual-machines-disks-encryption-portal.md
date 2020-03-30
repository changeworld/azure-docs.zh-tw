---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3ba5d74aa245fbcd9d43f2b4398387d7f59e202c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299484"
---
### <a name="portal"></a>入口網站

為磁片設置客戶管理的金鑰將需要您按特定順序創建資源（如果您是第一次這樣做）。 首先，您需要創建和設置 Azure 金鑰保存庫。

#### <a name="setting-up-your-azure-key-vault"></a>設置 Azure 金鑰保存庫

1. 登錄到[Azure 門戶](https://portal.azure.com/)並搜索金鑰保存庫
1. 搜索並選擇**金鑰保存庫**。

    [![sse-鍵-保存庫-門戶搜索.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure 金鑰保存庫、磁片加密集、VM、磁片和快照必須都位於同一區域中，並且要成功部署。

1. 選擇 **"添加"** 以創建新的金鑰保存庫。
1. 建立新的資源群組
1. 輸入金鑰保存庫名稱，選擇區域，然後選擇定價層。
1. 選擇 **"審閱 " 創建**"，驗證您的選擇，然後選擇 **"創建**"。

    ![Azure 金鑰保存庫創建體驗的螢幕截圖。 顯示您創建的特定值](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 金鑰保存庫完成部署後，選擇它。
1. 在 **"設置"** 下選擇 **"鍵**"。
1. 選擇 **"生成/導入"**

    !["金鑰保存庫"資源設置窗格的螢幕截圖。 在設置中顯示生成/導入按鈕。](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 將**金鑰類型**設置為**RSA**和**RSA 金鑰大小**設置為**2080**。
1. 根據需要填寫其餘選擇，然後選擇 **"創建**"。

    ![選擇生成/導入按鈕後出現的創建鍵邊欄選項卡的螢幕截圖](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>設置磁片加密集

要創建和配置磁片加密集，必須使用以下連結： https://aka.ms/diskencryptionsets。 如果您位於 Microsoft Azure 政府區域，則必須改用此連結： [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)。 磁片加密集創建在全域 Azure 門戶中尚不可用。

1. 打開適合您所在地區的磁片加密集連結：

    公共區域：[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 政府區域：[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. 選擇 **+添加**。

    ![磁片加密門戶主畫面的螢幕截圖。 突出顯示"添加"按鈕](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 選擇資源組，命名加密集，然後選擇與金鑰保存庫相同的區域。
1. 選擇**金鑰保存庫和金鑰**。
1. 選擇以前創建的金鑰保存庫和金鑰以及版本。
1. 按 [選取]****。
1. 選擇 **"審閱 " 創建**然後**創建**。

    ![磁片加密創建邊欄選項卡的螢幕截圖。 顯示訂閱、資源組、磁片加密集名稱、區域和金鑰保存庫 + 金鑰選擇器。](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成創建後打開磁片加密集，並選擇彈出的警報。

    ![警報快顯視窗的螢幕截圖："要將磁片、映射或快照與磁片加密集關聯，必須授予金鑰保存庫的許可權"。 選擇此警報以繼續](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

應彈出兩個通知並成功。 這樣做將允許您將磁片加密集與金鑰保存庫一起使用。

![金鑰保存庫成功許可權和角色指派的螢幕截圖。](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

現在，您已經創建並設置了金鑰保存庫和磁片加密集，您可以使用加密部署 VM。
VM 部署過程與標準部署過程類似，唯一的區別是您需要將 VM 部署到與其他資源相同的區域中，並且選擇使用客戶託管金鑰。

1. 打開適合您所在地區的磁片加密集連結：

    公共區域：[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 政府區域：[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. 搜索**虛擬機器**並選擇 **"添加"** 以創建 VM。
1. 在 **"基本"** 選項卡上，選擇與磁片加密集和 Azure 金鑰保存庫相同的區域。
1. 根據需要在 **"基本"** 選項卡上填寫其他值。

    ![VM 創建體驗的螢幕截圖，區域值突出顯示。](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在 **"磁片"** 選項卡上，**選擇使用客戶管理的金鑰靜態加密**。
1. 在**磁片加密集**下拉清單中選擇磁片加密集。
1. 根據需要進行其餘選擇。

    ![VM 創建體驗的螢幕截圖，磁片邊欄選項卡。 磁片加密集下拉清單突出顯示。](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>在現有磁片上啟用

要管理和配置現有磁片上的磁片加密，必須使用以下連結： https://aka.ms/diskencryptionsets。 在全域 Azure 門戶中尚未啟用現有磁片上的客戶管理金鑰。

> [!CAUTION]
> 在連接到 VM 的任何磁片上啟用磁片加密需要停止 VM。

1. 打開適合您所在地區的磁片加密集連結：

    公共區域：[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure 政府區域：[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. 導航到與磁片加密集之一位於同一區域的 VM。
1. 打開 VM 並選擇 **"停止**"。

    ![示例 VM 的主疊加螢幕截圖。 突出顯示"停止"按鈕](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. 在 VM 完成停止後，選擇**磁片**，然後選擇要加密的磁片。

    ![示例 VM 的螢幕截圖，打開磁片邊欄選項卡。 OS 磁片將突出顯示，作為要選擇的示例磁片。](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. 選擇 **"加密****"，然後選擇使用客戶管理的金鑰靜態加密**，然後在下拉清單中選擇磁片加密集。
1. 選取 [儲存]****。

    ![示例 OS 磁片的螢幕截圖。 加密邊欄選項卡處於打開狀態，選擇了使用客戶管理的金鑰進行靜態加密，以及示例 Azure 金鑰保存庫。 進行這些選擇後，將選擇保存按鈕。](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 對要加密的 VM 的任何其他磁片重複此過程。
1. 當磁片完成切換到客戶管理的金鑰後，如果沒有要加密的其他附加磁片，您可以啟動 VM。