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
ms.openlocfilehash: b8073240bdda38757a5e4feee66c9f54746966c4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632065"
---
### <a name="portal"></a>入口網站

為磁碟設定客戶管理的密鑰將需要您按特定順序創建資源(如果您是第一次這樣做)。 首先,您需要創建和設置 Azure 密鑰保管庫。

#### <a name="setting-up-your-azure-key-vault"></a>設定 Azure 金鑰保存庫

1. 登入[Azure 門戶](https://portal.azure.com/)。
1. 搜尋並選擇**金鑰保存庫**。

    [![sse-鍵-保管庫-門戶搜索.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure 密鑰保管庫、磁碟加密集、VM、磁碟和快照必須都位於同一區域中,並且要成功部署。

1. 選擇 **「新增」** 以建立新的密鑰保管庫。
1. 建立新的資源群組。
1. 輸入金鑰保管庫名稱,選擇區域,然後選擇定價層。
1. 選擇 **「審閱 」 建立**「,驗證您的選擇,然後選擇 **」創建**」。

    ![Azure 密鑰保管庫創建體驗的屏幕截圖。 顯示您建立的特定值](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 金鑰保管庫完成部署後,選擇它。
1. 在 **「設定」** 選擇 **「鍵**」 。
1. 選擇 **「生成/匯入**」 。

    !["金鑰保管庫"資源設置窗格的屏幕截圖。 在設置中顯示生成/導入按鈕。](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 將**金鑰類型**設定為**RSA**與**RSA 金鑰大小**設定為**2048**。
1. 根據需要填寫其餘選擇,然後選擇 **"創建**"。

    ![選擇產生/匯入按鈕後出現的建立鍵邊列選項卡的螢幕截圖](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>設定磁碟加密集

1. 搜索**磁碟加密集**並選擇它。
1. 在 **「磁碟加密集**」邊欄選項卡上選擇 **「添加**」 。

    ![磁片加密門戶主螢幕的螢幕截圖。 突顯「新增」按鈕](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 選擇資源組,命名加密集,然後選擇與密鑰保管庫相同的區域。
1. 選擇**金鑰保存與金鑰**。
1. 選擇以前創建的金鑰保管庫和密鑰以及版本。
1. 按 [選取]****。
1. 選擇 **「檢視」 建立**然後**建立**。

    ![磁碟加密創建邊欄選項卡的螢幕截圖。 顯示訂閱、資源組、磁碟加密集名稱、區域和密鑰保管庫 + 密鑰選擇器。](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成創建後打開磁碟加密集,並選擇彈出的警報。

    ![警報彈出視窗的螢幕截圖:「要將磁碟、映射或快照與磁碟加密集關聯,必須授予密鑰保管庫的許可權」。。 選擇此警報以繼續](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

應彈出兩個通知並成功。 這樣做將允許您將磁碟加密集與密鑰保管庫一起使用。

![密鑰保管庫成功許可權和角色分配的屏幕截圖。](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

現在,您已經創建並設置了密鑰保管庫和磁碟加密集,您可以使用加密部署 VM。
VM 部署過程與標準部署過程類似,唯一的區別是您需要將 VM 部署到與其他資源相同的區域中,並且選擇使用客戶託管金鑰。

1. 搜尋**虛擬機**並選擇 **「添加」** 以建立 VM。
1. 在 **「基本」** 選項卡上,選擇與磁碟加密集和 Azure 密鑰保管庫相同的區域。
1. 根據需要在 **「基本」** 選項卡上填寫其他值。

    ![VM 創建體驗的螢幕截圖,區域值突出顯示。](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在**磁碟選項**卡上,**選擇使用客戶管理的金鑰靜態加密**。
1. 在**磁碟加密集**下拉清單中選擇磁碟加密集。
1. 根據需要進行其餘選擇。

    ![VM 創建體驗的螢幕截圖,磁碟邊欄選項卡。 磁碟加密集下拉清單突出顯示。](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>在現有磁碟上開啟

> [!CAUTION]
> 在連接到 VM 的任何磁碟上啟用磁碟加密需要停止 VM。
    
1. 導航到與磁碟加密集之一位於同一區域的 VM。
1. 打開 VM 並選擇 **「停止**」。

    ![示例 VM 的主疊加螢幕截圖。 突顯「停止」按鈕](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. 在 VM 完成停止後,選擇**磁碟**,然後選擇要加密的磁碟。

    ![示例 VM 的螢幕截圖,打開磁碟邊欄選項卡。 OS 磁碟將突出顯示,作為要選擇的示例磁碟。](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. 選擇 **"加密****",然後選擇使用客戶管理的密鑰靜態加密**,然後在下拉清單中選擇磁碟加密集。
1. 選取 [儲存]  。

    ![示例OS磁碟的螢幕截圖。 加密邊欄選項卡處於打開狀態,選擇了使用客戶管理的密鑰進行靜態加密,以及示例 Azure 密鑰保管庫。 進行這些選擇后,將選擇保存按鈕。](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 對要加密的 VM 的任何其他磁碟重複此過程。
1. 當磁碟完成切換到客戶管理的密鑰後,如果沒有要加密的其他附加磁碟,您可以啟動 VM。
