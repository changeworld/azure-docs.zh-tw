---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013785"
---
存儲資源管理器 1.10.0 使使用者能夠上載、下載和複製託管磁片，以及創建快照。 由於這些附加功能，可以使用存儲資源管理器將資料從本地遷移到 Azure，並跨 Azure 區域遷移資料。

## <a name="prerequisites"></a>Prerequisites

要完成本文，您需要以下操作：
- Azure 訂用帳戶
- 一個或多個 Azure 託管磁片
- [Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)的最新版本

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

如果存儲資源管理器未連接到 Azure，您將無法使用它來管理資源。 本節將轉到 Azure 帳戶，以便可以使用存儲資源管理器管理資源。

1. 啟動 Azure 存儲資源管理器，然後按一下左側的**外掛程式**圖示。

    ![按一下外掛程式圖示](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. 選擇 **"添加 Azure 帳戶**"，然後按一下"**下一步**"。

    ![新增 Azure 帳戶](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. 在 **"Azure 登錄"對話方塊中**，輸入 Azure 憑據。

    ![Azure 登錄對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 從清單中選取您的訂用帳戶，然後按一下 [套用]****。

    ![選取您的訂用帳戶](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>從上置 VHD 上傳託管磁片

1. 在左側窗格中，展開**磁片**並選擇要將磁片上載到的資源組。

    ![選擇資源組 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 選取 [上傳]****。

    ![選取上傳](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. 在**上傳 VHD**中指定源 VHD、磁片名稱、作業系統類型、要將磁片上載到的區域以及帳戶類型。 在某些區域中，支援可用性區域，對於這些區域，您可以選擇您選擇的區域。
1. 選擇 **"創建**"以開始上傳磁片。

    ![上傳 vhd 對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 上載的狀態現在將顯示在 **"活動"中**。

    ![上傳狀態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 如果上載已完成，並且右窗格中看不到磁片，請選擇 **"刷新**"。

## <a name="download-a-managed-disk"></a>下載託管磁片

以下步驟說明如何將託管磁片下載到上置 VHD。 磁片的狀態必須**未連接**才能下載，不能下載**連接**的磁片。

1. 在左側窗格中，如果尚未展開磁片，請展開**磁片**並選擇要從中下載磁片的資源組。

    ![選擇資源組 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右側窗格中，選擇要下載的磁片。
1. 選擇 **"下載**"，然後選擇要保存磁片的位置。

    ![下載託管磁片](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. 選擇 **"保存**"，您的磁片將開始下載。 下載的狀態將顯示在 **"活動"** 中。

    ![下載狀態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>複製受控磁碟

使用存儲資源管理器，您可以在區域內或跨區域複製已處理的磁片。 要複製磁片：

1. 從左側的 **"磁片**"下拉清單，選擇包含要複製的磁片的資源組。

    ![選擇資源組 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右側窗格中，選擇要複製的磁片，然後選擇 **"複製**"。

    ![複製受控磁碟](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 在左側窗格中，選擇要將磁片粘貼到的資源組。

    ![選擇資源組 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 選擇 **"粘貼**"在右側窗格中。

    ![粘貼託管磁片](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. 在 **"粘貼磁片"** 對話方塊中，填寫值。 您還可以在受支援的區域指定可用性區域。

    ![粘貼磁片對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. 選擇 **"粘貼**"，您的磁片將開始複製，狀態將顯示在 **"活動"** 中。

    ![複製粘貼狀態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>建立快照集

1. 從左側的 **"磁片**"下拉清單，選擇包含要快照的磁片的資源組。

    ![選擇資源組 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右側，選擇要快照的磁片，然後選擇 **"創建快照**"。

    ![建立快照集](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. 在 **"創建快照**"中，指定快照的名稱以及要在其中創建快照的資源組。 然後選擇 **"創建**"。

    ![創建快照對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 創建快照後，可以選擇"**在活動中打開門戶**"以查看 Azure**Activities**門戶中的快照。

    ![門戶中打開快照](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>後續步驟
