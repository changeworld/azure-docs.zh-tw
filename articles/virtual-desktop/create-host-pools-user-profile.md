---
title: Windows 虛擬桌面 FSLogix 設定檔容器共用-Azure
description: 如何使用以虛擬機器為基礎的檔案共用，為 Windows 虛擬桌面主機集區設定 FSLogix 設定檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: a6bc8546a4047e921d62953e39eaddf546f38229
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388835"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>使用檔案共用建立主機集區的設定檔容器

Windows 虛擬桌面服務提供 FSLogix 設定檔容器作為建議的使用者設定檔解決方案。 我們不建議使用使用者設定檔磁片（UPD）解決方案，這會在未來的 Windows 虛擬桌上出版本中被取代。

本文將告訴您如何使用以虛擬機器為基礎的檔案共用，為主機集區設定 FSLogix 設定檔容器共用。 如需更 FSLogix 的檔，請參閱[FSLogix 網站](https://docs.fslogix.com/)。

>[!NOTE]
>如果您要尋找 Azure 上不同 FSLogix 設定檔容器儲存體選項的比較資料，請參閱[FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>建立將作為檔案共用的新虛擬機器

建立虛擬機器時，請務必將它放在與主機集區虛擬機器相同的虛擬網路上，或放置在與主機集區虛擬機器連線的虛擬網路上。 您可以使用多種方式來建立虛擬機器：

- [從 Azure 資源庫映射建立虛擬機器](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [從受控映射建立虛擬機器](../virtual-machines/windows/create-vm-generalized-managed.md)
- [從非受控映射建立虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

建立虛擬機器之後，請執行下列動作，將它加入網域：

1. 使用您在建立虛擬機器時提供的認證[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虛擬機器上，啟動 [**控制台**]，然後選取 [**系統**]。
3. 選取 [**電腦名稱稱**]，選取 [**變更設定**]，然後選取 [**變更 ...** ]
4. 選取 [**網域**]，然後在虛擬網路上輸入 Active Directory 網域。
5. 使用具有網域加入電腦許可權的網域帳戶進行驗證。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>準備虛擬機器作為使用者設定檔的檔案共用

以下是有關如何準備虛擬機器作為使用者設定檔之檔案共用的一般指示：

1. 將 Windows 虛擬桌面 Active Directory 使用者新增至[Active Directory 安全性群組](/windows/security/identity-protection/access-control/active-directory-security-groups/)。 此安全性群組將用來驗證您剛建立的檔案共用虛擬機器的 Windows 虛擬桌面使用者。
2. [連接到檔案共用虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
3. 在檔案共用虛擬機器上，于**C 磁片磁碟機**上建立將用來做為設定檔共用的資料夾。
4. 以滑鼠右鍵按一下新的資料夾，選取 [**屬性**]，選取 [**共用**]，然後選取 [ **Advanced 共用 ...** ]。
5. 選取 [**共用此資料夾**]，選取 [**許可權 ...** ]，然後選取 [**新增 ...** ]。
6. 搜尋您新增 Windows 虛擬桌面使用者的安全性群組，然後確定該群組具有 [**完全控制**]。
7. 新增安全性群組之後，以滑鼠右鍵按一下資料夾 **，選取 [** 內容]，選取 [**共用**]，然後複製**網路路徑**以供稍後使用。

如需許可權的詳細資訊，請參閱[FSLogix 檔](/fslogix/fslogix-storage-config-ht/)。

## <a name="configure-the-fslogix-profile-container"></a>設定 FSLogix 設定檔容器

若要使用 FSLogix 軟體設定虛擬機器，請在註冊至主機集區的每部機器上執行下列動作：

1. 使用您在建立虛擬機器時提供的認證[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 啟動網際網路瀏覽器，並流覽[至此連結](https://go.microsoft.com/fwlink/?linkid=2084562)以下載 FSLogix 代理程式。
3. 流覽至 .zip 檔案中的 \\\\Win32\\版本或 \\\\X64\\版本，然後執行**FSLogixAppsSetup**來安裝 FSLogix 代理程式。  若要深入瞭解如何安裝 FSLogix，請參閱[下載並安裝 FSLogix](/fslogix/install-ht/)。
4. 流覽至**Program Files** > **FSLogix** > **應用**程式，以確認代理程式已安裝。
5. 在 [開始] 功能表中，以系統管理員身分執行**RegEdit** 。 流覽至**電腦\\HKEY_LOCAL_MACHINE\\軟體\\FSLogix**。
6. 建立名為**Profiles**的金鑰。
7. 建立設定檔索引鍵的下列值：

| 名稱                | 類型               | 資料/值                        |
|---------------------|--------------------|-----------------------------------|
| 啟用             | DWORD              | 1                                 |
| VHDLocations        | 多字串值 | 「檔案共用的網路路徑」     |

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../security-center/security-center-just-in-time.md)。
