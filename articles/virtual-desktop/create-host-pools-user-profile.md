---
title: Windows 虛擬桌面 FSLogix 設定檔容器共用 - Azure
description: 如何使用基於虛擬機器的檔共用為 Windows 虛擬桌面主機池設置 FSLogix 設定檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250916"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>使用檔案共用建立主機集區的設定檔容器

Windows 虛擬桌面服務提供 FSLogix 設定檔容器作為推薦的使用者設定檔解決方案。 我們不建議使用使用者設定檔磁片 （UPD） 解決方案，該解決方案將在將來版本的 Windows 虛擬桌面中棄用。

本文將介紹如何使用基於虛擬機器的檔共用為主機池設置 FSLogix 設定檔容器共用。 有關更多 FSLogix 文檔，請參閱[FSLogix 網站](https://docs.fslogix.com/)。

>[!NOTE]
>如果要查找有關 Azure 上不同 FSLogix 設定檔容器存儲選項的比較材料，請參閱[FSLogix 設定檔容器的存儲選項](store-fslogix-profile.md)。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>創建新的虛擬機器，該虛擬機器將充當檔共用

創建虛擬機器時，請確保將其放置在與主機池虛擬機器相同的虛擬網路上，或者將其放置在與主機池虛擬機器連接的虛擬網路上。 您可以通過多種方式創建虛擬機器：

- [從 Azure 庫映射創建虛擬機器](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [從託管映射創建虛擬機器](../virtual-machines/windows/create-vm-generalized-managed.md)
- [從非託管映射創建虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

創建虛擬機器後，通過執行以下操作將其加入域：

1. 使用創建虛擬機器時提供的憑據[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虛擬機器上，啟動**控制台**並選擇 **"系統**"。
3. 選擇**電腦名稱稱**，選擇 **"更改設置**"，然後選擇 **"更改..."**
4. 選擇 **"域**"，然後在虛擬網路上輸入活動目錄域。
5. 使用具有域加入電腦許可權的域帳戶進行身份驗證。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>準備虛擬機器作為使用者設定檔的檔共用

以下是有關如何準備虛擬機器作為使用者設定檔的檔共用的一般說明：

1. 將 Windows 虛擬桌面活動目錄使用者添加到[活動目錄安全性群組](/windows/security/identity-protection/access-control/active-directory-security-groups/)。 此安全性群組將用於將 Windows 虛擬桌面使用者驗證為您剛剛創建的檔共用虛擬機器。
2. [連接到檔共用虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
3. 在檔共用虛擬機器上，在**C 磁碟機**上創建一個資料夾，該資料夾將用作設定檔共用。
4. 按右鍵新資料夾，選擇 **"屬性**"，選擇 **"共用**"，然後選擇 **"高級共用..."。**
5. 選擇 **"共用此資料夾**"，選擇 **"許可權..."，** 然後選擇 **"添加..."**
6. 搜索添加到 Windows 虛擬桌面使用者的安全性群組，然後確保該組具有**完全控制**。
7. 添加安全性群組後，按右鍵資料夾，選擇 **"屬性**"，選擇 **"共用**"，然後向下複製 **"網路路徑**"以用於以後。

有關許可權的詳細資訊，請參閱[FSLogix 文檔](/fslogix/fslogix-storage-config-ht/)。

## <a name="configure-the-fslogix-profile-container"></a>配置 FSLogix 設定檔容器

要使用 FSLogix 軟體配置虛擬機器，請對註冊到主機池的每台電腦執行以下操作：

1. 使用創建虛擬機器時提供的憑據[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 啟動互聯網瀏覽器並導航到[此連結](https://go.microsoft.com/fwlink/?linkid=2084562)以下載 FSLogix 代理。
3. 導航到\\\\.zip\\檔中的\\\\Win32 版本或 X64\\版本，並運行**FSLogixApps安裝程式**以安裝 FSLogix 代理。  要瞭解有關如何安裝 FSLogix 的更多內容，請參閱[下載並安裝 FSLogix](/fslogix/install-ht/)。
4. 導航到**程式檔** > **FSLogix** > **應用**以確認已安裝的代理。
5. 從"開始"功能表中，以管理員身份運行**RegEdit。** 導航到**電腦\\HKEY_LOCAL_MACHINE\\軟體\\FSLogix。**
6. 創建名為**設定檔**的鍵。
7. 為設定檔鍵創建以下值：

| 名稱                | 類型               | 資料/價值                        |
|---------------------|--------------------|-----------------------------------|
| 啟用             | DWORD              | 1                                 |
| VHD 位置        | 多字串值 | "檔共用的網路路徑"     |

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../security-center/security-center-just-in-time.md)。
