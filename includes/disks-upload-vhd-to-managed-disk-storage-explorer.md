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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74013785"
---
儲存體總管1.10.0 可讓使用者上傳、下載及複製受控磁片，以及建立快照集。 由於這些額外的功能，您可以使用儲存體總管將資料從內部部署遷移至 Azure，以及跨 Azure 區域遷移資料。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要下列專案：
- Azure 訂用帳戶
- 一或多個 Azure 受控磁片
- 最新版的[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

如果您的儲存體總管未連線到 Azure，則無法使用它來管理資源。 本節會將其連接到您的 Azure 帳戶，讓您可以使用儲存體總管來管理資源。

1. 啟動 Azure 儲存體總管，然後按一下左邊的**外掛程式**圖示。

    ![按一下外掛程式圖示](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. 選取 [**新增 Azure 帳戶**]，然後按 **[下一步]**。

    ![新增 Azure 帳戶](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. 在 [ **azure 登入**] 對話方塊中，輸入您的 Azure 認證。

    ![Azure 登入對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 從清單中選取您的訂用帳戶，然後按一下 [套用]****。

    ![選取您的訂用帳戶](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>從內部內部部署的 VHD 上傳受控磁片

1. 在左窗格中，展開 [**磁片**]，然後選取您想要上傳磁片的資源群組。

    ![選取資源群組1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 選取 [上傳]****。

    ![選取上傳](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. 在 **[上傳 vhd** ] 中，指定您的來源 VHD、磁片的名稱、OS 類型、您想要上傳磁片的區域，以及帳戶類型。 在某些區域中支援可用性區域，您可以在這些區域中選取您選擇的區域。
1. 選取 [**建立**] 以開始上傳您的磁片。

    ![上傳 vhd 對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 上傳的狀態現在會顯示在 [**活動**] 中。

    ![上傳狀態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 如果上傳已完成，而且您在右窗格中看不到磁片，**請選取 [** 重新整理]。

## <a name="download-a-managed-disk"></a>下載受控磁片

下列步驟說明如何將受控磁片下載至內部內部部署 VHD。 磁片的狀態必須是未**附加**的，才能下載，您無法下載**連接**的磁片。

1. 在左窗格中，如果尚未展開，請展開 [**磁片**]，然後選取您想要從中下載磁片的資源群組。

    ![選取資源群組1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右窗格中，選取您想要下載的磁片。
1. 選取 [**下載**]，然後選擇您要儲存磁片的位置。

    ![下載受控磁片](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. 選取 [**儲存**]，您的磁片就會開始下載。 下載的狀態會顯示在 [**活動**] 中。

    ![下載狀態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>複製受控磁碟

使用儲存體總管，您可以在區域內或跨區域複製受控磁片。 若要複製磁片：

1. 從左側的 [**磁片**] 下拉式清單中，選取包含您想要複製之磁片的資源群組。

    ![選取資源群組1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右窗格中，選取您想要複製的磁片，然後選取 [**複製**]。

    ![複製受控磁碟](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 在左窗格中，選取您想要貼上磁片的資源群組。

    ![選取資源群組2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 選取右窗格上的 [**貼**上]。

    ![貼上受控磁片](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. 在 [**貼上磁片**] 對話方塊中，填入值。 您也可以在支援的區域中指定可用性區域。

    ![[貼上磁片] 對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. 選取 [**貼**上]，您的磁片就會開始複製，狀態會顯示在 [**活動**] 中。

    ![複製貼上狀態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>建立快照集

1. 從左側的 [**磁片**] 下拉式清單中，選取包含您想要快照集之磁片的資源群組。

    ![選取資源群組1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 在右側，選取您想要快照集的磁片，然後選取 [**建立快照**集]。

    ![建立快照集](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. 在 [**建立快照**集] 中，指定快照集的名稱以及您想要在其中建立的資源群組。 然後選取 [建立]  。

    ![建立快照集對話方塊](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 建立快照集之後，您可以選取 [**活動**] 中的 [**在入口網站中開啟**]，以在 Azure 入口網站中查看快照集。

    ![在入口網站中開啟快照集](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>後續步驟
