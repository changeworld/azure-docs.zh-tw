---
title: 教學課程 - 使用 Azure 檔案同步擴充 Windows 檔案伺服器 | Microsoft Docs
description: 通盤了解如何使用 Azure 檔案同步擴充 Windows 檔案伺服器。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 80fcebec76788ca9ec754b35c57f9965f38c2c0e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037094"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>教學課程：使用 Azure 檔案同步擴充 Windows 檔案伺服器

本文示範使用 Azure 檔案同步來擴充 Windows Server 儲存體容量的基本步驟。雖然本教學課程使用作為 Azure 虛擬機器 (VM) 的 Windows Server，但您通常會對內部部署伺服器進行此程序。 您可以在[部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)一文中找到在自己的環境中部署 Azure 檔案同步的指示。

> [!div class="checklist"]
> * 部署儲存體同步服務
> * 準備 Windows Server 以搭配 Azure 檔案同步使用
> * 安裝 Azure 檔案同步代理程式
> * 向儲存體同步服務註冊 Windows Server
> * 建立同步群組和雲端端點
> * 建立伺服器端點

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="prepare-your-environment"></a>準備您的環境

在本教學課程中，您必須先完成下列動作，才能部署 Azure 檔案同步：

- 建立 Azure 儲存體帳戶和檔案共用
- 設定 Windows Server 2016 Datacenter VM
- 準備要使用 Azure 檔案同步的 Windows Server VM

### <a name="create-a-folder-and-txt-file"></a>建立資料夾和 .txt 檔案

在本機電腦上，建立名為 FilesToSync  的新資料夾，並新增名為 mytestdoc.txt  的文字檔。 您將在本教學課程稍後的步驟中將該檔案上傳至檔案共用。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>建立檔案共用

部署 Azure 儲存體帳戶之後，您將建立檔案共用。

1. 在 Azure 入口網站中，選取 [前往資源]  。
1. 在 [儲存體帳戶] 窗格中選取 [檔案]  。

    ![選取檔案](./media/storage-sync-files-extend-servers/click-files.png)

1. 選取 [+ 檔案共用]  。

    ![選取 [新增檔案共用] 按鈕](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. 將新的檔案共用命名為 afsfileshare  。 輸入 "1" 作為 [配額]  ，然後選取 [建立]  。 配額上限為 5 TiB，但本教學課程只需要 1 GB。

    ![提供新檔案共用的名稱和配額](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. 選取新的檔案共用。 在檔案共用位置上，選取 [上傳]  。

    ![上傳檔案](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. 瀏覽至 .txt 檔案建立所在的 FilesToSync  資料夾，選取 mytestdoc.txt  ，然後選取 [上傳]  。

    ![瀏覽檔案共用](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

此時，您已建立儲存體帳戶，和具有一個檔案的檔案共用。 現在，您將部署 Windows Server 2016 Datacenter 的 Azure VM，以代表本教學課程中的內部部署伺服器。

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>部署 VM 並連結資料磁碟

1. 移至 Azure 入口網站，並展開左側的功能表。 選擇位於左上角的 [建立資源]  。
1. 在 **Azure Marketplace** 資源清單上方的搜尋方塊中，搜尋 **Windows Server 2016 Datacenter**，並在結果中加以選取。 選擇 [建立]  。
1. 移至 [基本]  索引標籤。在 [專案詳細資料]  下方，選取您為本教學課程建立的資源群組。

   ![在入口網站刀鋒視窗上輸入關於 VM 的基本資訊](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. 在 [執行個體詳細資料]  下方，提供 VM 名稱。 例如，您可以使用 myVM  。
1. 請勿變更 [區域]  、[可用性選項]  、[映像]  及 [大小]  的預設設定。
1. 在 [系統管理員帳戶]  底下提供 VM 的 [使用者名稱]  和 [密碼]  。
1. 在 [輸入連接埠規則]  下方，選擇 [允許選取的連接埠]  ，然後從下拉式功能表中選取 [RDP (3389)]  和 [HTTP]  。

1. 在建立 VM 之前，您需要先建立資料磁碟。

   1. 選取 [下一步：磁碟]  。

      ![新增資料磁碟](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. 在 [磁碟]  索引標籤的 [磁碟選項]  下方，保留預設值。
   1. 在 [資料磁碟]  下方，選取 [建立並連結新磁碟]  。

   1. 在本教學課程中請使用預設設定，但 [大小 (GiB)]  除外，可以變更為 [1 GB]  。

      ![資料磁碟詳細資料](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. 選取 [確定]  。
1. 選取 [檢閱 + 建立]  。
1. 選取 [建立]  。

   您可以選取 [通知]  圖示以監看**部署進度**。 建立新 VM 可能需要幾分鐘的時間。

1. 在 VM 部署完成後，請選取 [前往資源]  。

   ![前往資源](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

此時，您已建立新的虛擬機器，並連結資料磁碟。 接著，您應連線至 VM。

### <a name="connect-to-your-vm"></a>連接到您的 VM

1. 在 Azure 入口網站中，於虛擬機器的 [屬性] 頁面上選取 [連線]  。

   ![從入口網站連線至 Azure VM](./media/storage-sync-files-extend-servers/connect-vm.png)

1. 在 [連線至虛擬機器]  頁面上保留預設選項，以使用 **IP 位址**透過連接埠 3389 進行連線。 選取 [下載 RDP 檔案]  。

   ![下載 RDP 檔案](./media/storage-sync-files-extend-servers/download-rdp.png)

1. 開啟下載的 RDP 檔案，然後在出現提示時選取 [連線]  。
1. 在 [Windows 安全性]  視窗中，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 輸入 localhost\username  作為使用者名稱，並輸入您為虛擬機器建立的密碼，然後選取 [確定]  。

   ![更多選擇](./media/storage-sync-files-extend-servers/local-host2.png)

1. 您可能會在登入過程中收到憑證警告。 選取 [是]  或 [繼續]  以建立連線。

### <a name="prepare-the-windows-server"></a>準備 Windows Server

針對 Windows Server 2016 Datacenter 伺服器，請停用 [Internet Explorer 增強式安全性設定]。 此步驟只需要在初始伺服器註冊時執行。 您可以在註冊伺服器後重新啟用它。

在 Windows Server 2016 Datacenter VM 中，伺服器管理員會自動開啟。  如果伺服器管理員未依預設開啟，請在「開始功能表」中加以搜尋。

1. 在 [伺服器管理員]  中，選取 [本機伺服器]  。

   ![位於伺服器管理員 UI 左側的 [本機伺服器]](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. 選取 [屬性]  窗格上，選取 [IE 增強式安全性設定]  的連結。  

    ![位於伺服器管理員 UI 中的 [IE 增強式安全性設定] 窗格](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. 在 [Internet Explorer 增強式安全性設定]  對話方塊中，針對 [系統管理員]  和 [使用者]  選取 [關閉]  。

    ![已選取 [關閉] 的 [Internet Explorer 增強式安全性設定] 快顯視窗](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

現在，您可以在 VM 中新增資料磁碟。

### <a name="add-the-data-disk"></a>新增資料磁碟

1. 在仍位於 [Windows Server 2016 Datacenter]  VM 的情況下，選取 [檔案和儲存體服務]   > [磁碟區]   > [磁碟]  。

    ![資料磁碟](media/storage-sync-files-extend-servers/your-disk.png)

1. 以滑鼠右鍵按一下名為 **Msft 虛擬磁碟**的 1 GB 磁碟，然後按一下 [新增磁碟區]  。
1. 完成精靈。 使用預設設定，並記下指派的磁碟機代號。
1. 選取 [建立]  。
1. 選取 [關閉]  。

   此時，您已將磁碟上線，並建立磁碟區。 在 Windows Server VM 中開啟檔案總管，以確認最近新增的資料磁碟存在於其中。

1. 在 VM 的檔案總管中展開 [此電腦]  ，然後開啟新的磁碟機。 在此範例中，其為 F: 磁碟機。
1. 以滑鼠右鍵按一下並選取 [新增]   > [資料夾]  。 將資料夾命名為 FilesToSync  。
1. 開啟 **FilesToSync** 資料夾。
1. 以滑鼠右鍵按一下並選取 [新增]   > [文字文件]  。 將文字檔命名為 MyTestFile  。

    ![新增文字檔](media/storage-sync-files-extend-servers/new-file.png)

1. 關閉**檔案總管**和**伺服器管理員**。

### <a name="download-the-azure-powershell-module"></a>下載 Azure PowerShell 模組

接下來，在 Windows Server 2016 Datacenter VM 中，將 Azure PowerShell 模組安裝在伺服器上。

1. 在 VM 中，開啟已提升權限的 PowerShell 視窗。
1. 執行以下命令：

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > 如果您的 NuGet 版本早於 2.8.5.201，系統會提示您下載並安裝最新版的 NuGet。

   根據預設，PowerShell 資源庫未設為 PowerShellGet 的信任存放庫。 第一次使用 PSGallery 時，您會看到下列提示：

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. 請回答 [是]  或 [全部皆是]  以繼續安裝。

`Az` 模組是 Azure PowerShell Cmdlet 的彙總套件模組。 安裝此項目會下載所有可用的 Azure Resource Manager 模組，並使這些模組的 Cmdlet 可供使用。

此時，您已設定教學課程所需的環境。 您已準備就緒，可部署儲存體同步服務。

## <a name="deploy-the-service"></a>部署服務

若要部署 Azure 檔案同步，首先必須將**儲存體同步服務**資源放到所選訂用帳戶的資源群組中。 儲存體同步服務會從其訂用帳戶和資源群組繼承存取權限。

1. 在 Azure 入口網站中選取 [建立資源]  ，然後搜尋 [Azure 檔案同步]  。
1. 在搜尋結果中，選取 [Azure 檔案同步]  。
1. 選取 [建立]  來開啟 [部署儲存體同步]  索引標籤。

   ![部署儲存體同步](media/storage-sync-files-extend-servers/afs-info.png)

   在開啟的窗格中，輸入下列資訊：

   | 值 | 描述 |
   | ----- | ----- |
   | **名稱** | 儲存體同步服務的唯一名稱 (每一訂用帳戶)。<br><br>在本教學課程中，請使用 afssyncservice02  。 |
   | **訂用帳戶** | 您在本教學課程中使用的 Azure 訂用帳戶。 |
   | **資源群組** | 包含儲存體同步服務的資源群組。<br><br>在本教學課程中，請使用 afsresgroup101918  。 |
   | **位置** | 美國東部 |

1. 完成時，請選取 [建立]  來部署**儲存體同步服務**。
1. 選取 [通知]  索引標籤 > [前往資源]  。

## <a name="install-the-agent"></a>安裝代理程式

Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。

1. 在 **Windows Server 2016 Datacenter** VM 中，開啟 **Internet Explorer**。
1. 前往 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)。 向下捲動至 [Azure 檔案同步代理程式]  區段，然後選取 [下載]  。

   ![同步代理程式下載](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. 選取 [StorageSyncAgent_V3_WS2016.EXE]  的核取方塊，然後選取 [下一步]  。

   ![選取代理程式](media/storage-sync-files-extend-servers/select-agent.png)

1. 選取 [允許一次]   > [執行]   > [開啟]  。
1. 如果尚未這麼做，請關閉 PowerShell 視窗。
1. 接受 [儲存體同步代理程式安裝精靈]  中的預設值。
1. 選取 [安裝]  。
1. 選取 [完成]  。

您已在 Windows Server 2016 Datacenter VM 上部署 Azure Sync 服務並安裝代理程式。 現在，您必須向儲存體同步服務註冊 VM。

## <a name="register-windows-server"></a>註冊 Windows Server

向儲存體同步服務註冊 Windows Server，會在您的伺服器 (或叢集) 與儲存體同步服務之間建立信任關係。 一個伺服器只能註冊至一個儲存體同步服務。 伺服器可以與該儲存體同步服務的其他相關伺服器和 Azure 檔案共用同步。

在您安裝 Azure 檔案同步代理程式之後，伺服器註冊 UI 應該會自動開啟。 如果沒有，您可以從其檔案位置手動加以開啟：`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. 當伺服器註冊 UI 在 VM 中開啟時，請選取 [確定]  。
1. 選取 [登入]  開始作業。
1. 使用 Azure 帳戶認證進行登入，然後選取 [登入]  。
1. 請提供下列資訊：

   ![伺服器註冊 UI 的螢幕擷取畫面](media/storage-sync-files-extend-servers/signin.png)

   | 值 | 描述 |
   | ----- | ----- |
   | **Azure 訂用帳戶** | 包含本教學課程所用儲存體同步服務的訂用帳戶。 |
   | **資源群組** | 包含儲存體同步服務的資源群組。 在本教學課程中，請使用 afsresgroup101918  。 |
   | **儲存體同步服務** | 儲存體同步服務的名稱。 在本教學課程中，請使用 afssyncservice02  。 |

1. 選取 [註冊]  以完成伺服器註冊。
1. 在註冊過程中，系統會提示您再登入一次。 登入並選取 [下一步]  。
1. 選取 [確定]  。

## <a name="create-a-sync-group"></a>建立同步群組

同步群組定義一組檔案的同步拓撲。 同步群組必須包含一個雲端端點，代表 Azure 檔案共用。 同步群組也必須包含一或多個伺服器端點。 伺服器端點代表已註冊伺服器上的路徑。 若要建立同步群組：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，從儲存體同步服務中選取 [+ 同步群組]  。 在本教學課程中，請使用 afssyncservice02  。

   ![在 Azure 入口網站中建立新的同步群組](media/storage-sync-files-extend-servers/add-sync-group.png)

1. 輸入下列資訊，以建立具有雲端端點的同步群組：

   | 值 | 描述 |
   | ----- | ----- |
   | **同步群組名稱** | 此名稱在儲存體同步服務中必須是唯一的，但可以是任何對您而言合理的名稱。 在本教學課程中，請使用 afssyncgroup  。|
   | **訂用帳戶** | 本教學課程的儲存體同步服務部署所在的訂用帳戶。 |
   | **儲存體帳戶** | 選擇 [選取儲存體帳戶]  。 在出現的窗格中，選取您先前建立的 Azure 檔案共用所屬的儲存體帳戶。 在本教學課程中，請使用 afsstoracct101918  。 |
   | **Azure 檔案共用** | 您先前建立的 Azure 檔案共用的名稱。 在本教學課程中，請使用 afsfileshare  。 |

1. 選取 [建立]  。

如果您選取同步群組，就會發現您現在擁有一個**雲端端點**。

## <a name="add-a-server-endpoint"></a>新增伺服器端點

伺服器端點代表已註冊伺服器上的特定位置。 例如，伺服器磁碟區上的資料夾。 若要新增伺服器端點：

1. 選取新建立的同步群組，然後選取 [新增伺服器端點]  。

   ![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. 在 [新增伺服器端點]  窗格上輸入下列資訊，以建立伺服器端點：

   | 值 | 描述 |
   | ----- | ----- |
   | **已註冊的伺服器** | 您先前建立的伺服器名稱。 在本教學課程中，請使用 afsvm101918  。 |
   | **路徑** | 您先前建立之磁碟機的 Windows Server 路徑。 在本教學課程中，請使用 f:\filestosync  。 |
   | **雲端階層處理** | 在本教學課程中保持停用。 |
   | **磁碟區可用空間** | 在本教學課程中保持空白。 |

1. 選取 [建立]  。

您的檔案現在會在 Azure 檔案共用和 Windows Server 之間保持同步。

![已成功同步處理 Azure 儲存體](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解使用 Azure 檔案同步來擴充 Windows Server 儲存體容量的基本步驟。若要深入了解如何規劃 Azure 檔案同步的部署，請參閱：

> [!div class="nextstepaction"]
> [規劃 Azure 檔案同步部署](./storage-sync-files-planning.md)
