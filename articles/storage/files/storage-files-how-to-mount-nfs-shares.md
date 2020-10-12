---
title: 掛接 Azure NFS 檔案共用-Azure 檔案儲存體
description: 瞭解如何掛接網路檔案系統共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 530ae82720e6b4eb6a3e4d1021c0b37b9f4dbf5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707436"
---
# <a name="how-to-mount-an-nfs-file-share"></a>如何掛接 NFS 檔案共用

[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 您可以使用 Server Message Block protocol (SMB) 或 Network File System (NFS) 通訊協定，將 Azure 檔案共用掛接在 Linux 發行版本中。 本文著重于裝載 NFS，如需使用 SMB 掛接的詳細資訊，請參閱搭配 [使用 Azure 檔案儲存體與 Linux](storage-how-to-use-files-linux.md)。 如需每個可用通訊協定的詳細資訊，請參閱 [Azure 檔案共用通訊協定](storage-files-compare-protocols.md)。

## <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>區域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>必要條件

- [建立 NFS 共用](storage-files-how-to-create-nfs-shares.md)。

    > [!IMPORTANT]
    > NFS 共用只能從信任的網路存取。 與您的 NFS 共用的連線必須來自下列其中一個來源：

- 使用下列其中一種網路解決方案：
    - [建立私人端點](storage-files-networking-endpoints.md#create-a-private-endpoint) (建議的) 或[限制對公用端點的存取](storage-files-networking-endpoints.md#restrict-public-endpoint-access)。
    - [在 Linux 上設定點對站 (P2S) VPN，以搭配 Azure 檔案儲存體使用](storage-files-configure-p2s-vpn-linux.md)。
    - [設定與 Azure 檔案儲存體搭配使用的站對站 VPN](storage-files-configure-s2s-vpn.md)。
    - 設定 [ExpressRoute](../../expressroute/expressroute-introduction.md)。

## <a name="disable-secure-transfer"></a>停用安全傳輸

1. 登入 Azure 入口網站，並存取包含您所建立之 NFS 共用的儲存體帳戶。
1. 選取**組態**。
1. 選取 [ **已停用** ]，表示 **需要安全傳輸**。
1. 選取 [儲存]****。

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="停用安全傳輸的儲存體帳戶設定畫面螢幕擷取畫面。":::

## <a name="mount-an-nfs-share"></a>掛接 NFS 共用

1. 建立檔案共用之後，請選取該共用，然後選取 [ **從 Linux 連線]**。
1. 輸入您想要使用的掛接路徑，然後複製腳本。
1. 連接至您的用戶端，並使用提供的裝載腳本。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="停用安全傳輸的儲存體帳戶設定畫面螢幕擷取畫面。":::

您現在已裝載 NFS 共用。

## <a name="next-steps"></a>後續步驟

- 透過我們的文章、 [規劃 Azure 檔案儲存體部署](storage-files-planning.md)，深入瞭解 Azure 檔案儲存體。
- 如果您遇到任何問題，請參閱 [疑難排解 AZURE NFS 檔案共用](storage-troubleshooting-files-nfs.md)。