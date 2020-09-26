---
title: 透過 macOS 的 SMB 掛接 Azure 檔案共用 | Microsoft Docs
description: 瞭解如何使用搜尋工具或終端機，透過 macOS 透過 SMB 掛接 Azure 檔案共用。 Azure 檔案服務是 Microsoft 易於使用的雲端檔案系統。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326060"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>透過 macOS 的 SMB 掛接 Azure 檔案共用
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 Azure 檔案共用可以使用業界標準的 SMB 3 通訊協定來掛接，方法是 macOS 高塞拉里昂 10.13 +。 本文說明兩種在 macOS 上掛接 Azure 檔案共用的不同方式：使用 Finder UI 和使用終端機。

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>在 macOS 上掛接 Azure 檔案共用的必要條件
* **儲存體帳戶名稱**：若要掛接 Azure 檔案共用，您將需要儲存體帳戶的名稱。

* **儲存體帳戶金鑰**：若要掛接 Azure 檔案共用，您將需要主要 (或次要) 儲存體金鑰。 掛接目前不支援 SAS 金鑰。

* **請確定已開啟連接埠 445**SMB 透過 TCP 通訊埠 445 進行通訊。 在用戶端電腦 (Mac) 中，請檢查並確定您的防火牆不會封鎖 TCP 通訊埠 445。

## <a name="mount-an-azure-file-share-via-finder"></a>透過搜尋工具掛接 Azure 檔案共用
1. **開啟搜尋工具**：搜尋工具在 macOS 中為預設開啟，但是您可以按一下 Dock 上的 [macOS 臉部圖示] 確保它是目前選取的應用程式：  
    ![macOS 臉部圖示](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **從 [執行] 功能表中選取 [連接到伺服器**]：使用必要條件的 UNC 路徑，將開頭的雙反斜線 (`\\`) 轉換為 `smb://` 和其他所有反斜線 (`\`) ，將斜線轉寄 (`/`) 。 您的連結應看起來如下所示：![[連線至伺服器] 對話方塊](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **提示您輸入使用者名稱和密碼時，使用儲存體帳戶名稱和儲存體帳戶金鑰**：當您在 [連線至伺服器] 對話方塊中按一下 [連線] 時，系統會提示您的使用者名稱和密碼 (這會使用您的 macOS 使用者名稱自動填入)。 您可以選擇將儲存體帳戶名稱/儲存體帳戶金鑰置於您的 macOS 金鑰鏈。

4. **視需要使用 Azure 檔案共用**：在使用共用名稱和儲存體帳戶金鑰替換使用者名稱和密碼之後，系統將掛接共用。 您可以依照平常使用本機資料夾/檔案共用的方式使用，包括將檔案拖放到檔案共用中：

    ![已掛接的 Azure 檔案共用快照集](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>透過終端機掛接 Azure 檔案共用
1. 將  `<storage-account-name>` 、 `<storage-account-key>` 和取代為 `<share-name>`   您環境的適當值。

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **視需要使用 Azure 檔案共用**：Azure 檔案共用將會掛接於先前命令所指定的掛接點。  

    ![已掛接的 Azure 檔案共用快照集](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>後續步驟
* [將您的 Mac 連線到共用的電腦和伺服器-Apple 支援](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)