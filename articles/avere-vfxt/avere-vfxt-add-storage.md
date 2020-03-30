---
title: 設定 Avere vFXT 儲存體 - Azure
description: 如何將後端儲存體系統新增至「適用於 Azure 的 Avere vFXT」
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252606"
---
# <a name="configure-storage"></a>設定儲存體

此步驟會為您的 vFXT 叢集設定後端儲存體系統。

> [!TIP]
> 如果與 Avere vFXT 群集一起創建新的 Azure Blob 容器，則該容器已配置並可供使用。

如果您未隨著 Avere vFXT 叢集建立新的 Blob 容器，或是想要新增額外的硬體或雲端型儲存體系統，請依照下列指示進行操作。

主要工作有兩個：

1. [創建一個核心檔程式](#create-a-core-filer)，該檔程式將 vFXT 群集連接到現有存儲系統或 Azure 存儲帳戶容器。

1. [建立命名空間連接點](#create-a-junction)，這會定義用戶端將掛接的路徑。

這些步驟會使用「Avere 控制台」。 請參閱[存取 vFXT 叢集](avere-vfxt-cluster-gui.md)，以了解如何使用它。

## <a name="create-a-core-filer"></a>建立核心檔案管理工具

"核心檔案伺服器"是後端存儲系統的 vFXT 術語。 此儲存體可以是硬體 NAS 設備 (例如 NetApp 或 Isilon)，也可以是雲端物件存放區。 有關核心檔案伺服器的詳細資訊，請參閱[Avere 群集設置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)。

若要新增核心檔案管理工具，請選擇兩個主要類型的核心檔案管理工具其中之一：

* [NAS 核心檔案管理工具](#nas-core-filer) - 說明如何新增 NAS 核心檔案管理工具
* [Azure 存儲雲核心檔程式](#azure-blob-storage-cloud-core-filer)- 介紹如何將 Azure Blob 存儲容器添加為雲核心檔案伺服器

### <a name="nas-core-filer"></a>NAS 核心檔案管理工具

NAS 核心檔案伺服器可以是本地 NetApp 或 Isilon 設備，也可以是雲中的 NAS 終結點。 儲存體系統必須具備與 Avere vFXT 叢集的可靠高速連線 - 例如 1 GBps ExpressRoute 連線 (不是 VPN) - 而且必須將所要使用之 NAS 匯出項的根存取權授與叢集。

按照以下步驟添加 NAS 核心檔案伺服器：

1. 從「Avere 控制台」中，按一下頂端的 [Settings] \(設定\)**** 索引標籤。

1. 按一下左側**的核心檔案伺服器** > **管理核心檔案伺服器**。

1. 按一下 **[建立]**。

   ![新增核心檔案管理工具頁面的螢幕擷取畫面，其中游標置於 [建立] 按鈕上](media/avere-vfxt-add-core-filer-start.png)

1. 在精靈中填寫必要資訊：

   * 為核心檔案管理工具命名。
   * 提供完整網域名稱 (FQDN) (如果有的話)。 否則，請提供解析成您核心檔案管理工具的 IP 位址或主機名稱。
   * 從清單中選擇您的檔案管理工具類別。 如果不確定，請選擇 [Other] \(其他\)****。

     ![含有核心檔案管理工具名稱及其完整網域名稱的 [Add new core filer] \(新增核心檔案管理工具\) 頁面螢幕擷取畫面](media/avere-vfxt-add-core-filer.png)
  
   * 按 [Next] \(下一步\)****，然後選擇快取原則。
   * 按一下 [Add Filer] \(新增檔案管理工具\)****。
   * 如需更多詳細資訊，請參閱 Avere 叢集設定指南中的[新增 NAS 核心檔案管理工具](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) \(英文\)。

接著，繼續進行[建立連接點](#create-a-junction)。  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob 存儲雲核心檔案伺服器

要使用 Azure Blob 存儲作為 vFXT 群集的後端存儲，需要一個空容器作為核心檔程式添加。

將 Blob 儲存體新增到您的叢集需要執行下列步驟：

* 建立儲存體帳戶 (下方的步驟 1)
* 建立空的 Blob 容器 (步驟 2-3)
* 新增儲存體存取金鑰作為 vFXT 叢集的雲端認證 (步驟 4-6)
* 新增 Blob 容器作為 vFXT 叢集的核心檔案管理工具 (步驟 7-9)
* 建立用戶端用來掛接核心檔案管理工具的命名空間連接點 ([建立連接點](#create-a-junction)，硬體和雲端儲存體都相同)

> [!TIP]
> 如果在為 Azure 群集創建 Avere vFXT 時創建新的 Blob 容器，則部署範本會自動將容器配置為核心檔程式。 （如果使用創建腳本，則也是如此，該腳本可應請求提供。以後不需要配置核心檔案伺服器。
>
> 群集創建工具為您執行以下配置任務：
>
> * 在提供的存儲帳戶中創建新的 Blob 容器
> * 將容器定義為核心檔案伺服器
> * 創建到容器的命名空間交匯點
> * 在群集的虛擬網路內創建存儲服務終結點

若要在建立叢集後新增 Blob 儲存體，請依照下列步驟進行操作。

1. 建立含有下列設定的一般用途 v2 儲存體帳戶：

   * **訂用帳戶** - 與 vFXT 叢集相同
   * **資源群組** - 與 vFXT 叢集群組相同 (選擇性)
   * **位置** - 與 vFXT 叢集相同
   * **效能** - 標準 (不支援進階儲存體)
   * **帳戶種類** - 一般用途 V2 (StorageV2)
   * **複寫** - 本地備援儲存體 (LRS)
   * **存取層** - 經常性
   * **需要安全傳輸** - 請停用此選項 (非預設值)
   * **虛擬網路** - 不需要

   您可以使用 Azure 入口網站或按一下下方的 [Deploy to Azure] \(部署至 Azure\) 按鈕。

   [![創建存儲帳戶的按鈕](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. 建立帳戶之後，瀏覽至儲存體帳戶頁面。

   ![Azure 入口網站中的新儲存體帳戶](media/avere-vfxt-new-storage-acct.png)

1. 創建新的 Blob 容器：按一下概覽頁面上的**容器**，然後按一下 **"容器**"。 使用任何容器名稱，並請務必將存取權設定為 [私用]****。

   ![存儲 blob 頁，其中已圈出 _容器按鈕，並在快顯視窗中創建新容器](media/avere-vfxt-new-blob.png)

1. 按一下 **"設置**"下的 **"訪問金鑰**"獲取 Azure 存儲帳戶金鑰。 複製提供的金鑰之一。

   ![用於複製金鑰的 Azure 入口網站 GUI](media/avere-vfxt-copy-storage-key.png)

1. 開啟您叢集的「Avere 控制台」。 按一下 [Settings] \(設定\)****，然後開啟左側導覽窗格上的 [Cluster] \(叢集\)**** > [Cloud Credentials] \(雲端認證\)****。 在 [Cloud Credentials] \(雲端認證\) 頁面上，按一下 [Add Credential] \(新增認證\)****。

   ![按一下 [Cloud Credentials] \(雲端認證\) 設定頁面上的 [Add Credential] \(新增認證\) 按鈕](media/avere-vfxt-new-credential-button.png)

1. 填入下列資訊來為雲端核心檔案管理工具建立認證：

   | 欄位 | 值 |
   | --- | --- |
   | Credential name (認證名稱) | 任何描述性的名稱 |
   | 服務類型 | （選擇 Azure 存儲訪問金鑰） |
   | 租用戶 | 儲存體帳戶名稱 |
   | 訂用帳戶 | 訂用帳戶識別碼 |
   | Storage Access Key (儲存體存取金鑰) | Azure 儲存體帳戶金鑰 (在上一個步驟中所複製) |

   按一下 [提交]****。

   ![「Avere 控制台」中已完成的雲端認證表單](media/avere-vfxt-new-credential-submit.png)

1. 接著，建立核心檔案管理工具。 在 Avere 控制台的左側，按一下 **"核心檔案伺服器** >  **管理核心檔器**"。

1. 按一下 [Manage Core Filers] \(管理核心檔案管理工具\)**** 設定頁面上的 [Create] \(建立\)****。

1. 填寫精靈欄位：

   * 選取 [Cloud] \(雲端\)**** 檔案管理工具類型。
   * 為新的核心檔案管理工具命名，然後按 [Next] \(下一步\)****。
   * 接受預設的快取原則，然後繼續進行第三頁。
   * 在 [Service type] \(服務類型\)**** 中，選擇 [Azure storage] \(Azure 儲存體)\****。
   * 選擇稍早建立的認證。
   * 將 [Bucket contents] \(貯體內容\)**** 設定為 [Empty] \(空白\)****
   * 將 [Certificate verification] \(憑證驗證\)**** 變更為 [Disabled] \(已停用\)****
   * 將 [Compression mode] \(壓縮模式\)**** 變更為 [None] \(無\)****
   * 按 [下一步]****。
   * 在第四頁上，於 [Bucket name] \(貯體名稱\)**** 中將容器名稱輸入為 *storage_account_name*/*container_name*。
   * 視需要將 [Encryption type] \(加密類型\)**** 設定為 [None] \(無\)****。  「Azure 儲存體」預設會經過加密。
   * 按一下 [Add Filer] \(新增檔案管理工具\)****。

   如需更多詳細資訊，請參閱 Avere 叢集設定指南中的[新增雲端核心檔案管理工具](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) \(英文\)。

頁面將會重新整理，或是您也可以重新整理頁面來顯示您的新核心檔案管理工具。

接著，您必須[建立連接點](#create-a-junction)。

## <a name="create-a-junction"></a>建立連接點

連接點是您為用戶端建立的路徑。 用戶端會掛接該路徑並抵達您選擇的目的地。

例如，您可以建立 `/vfxt/files` 來對應至您的 NetApp 核心檔案管理工具 `/vol0/data` 匯出項和 `/project/resources` 子目錄。

如需有關連接點的詳細資訊，請參閱 [Avere 叢集設定指南的命名空間小節](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) \(英文\)。

按照 Avere 控制台介面中的以下步驟操作：

* 按一下左上角的**VServer** > **命名空間**。
* 提供以 / (斜線) 開頭的命名空間路徑，例如 ``/vfxt/data``。
* 選擇您的核心檔案管理工具。
* 選擇核心檔案管理工具匯出項。
* 按 [下一步]****。

  ![已完成連接點、核心檔案管理工具及匯出項欄位的 [Add new junction] \(新增連接點\) 頁面螢幕擷取畫面](media/avere-vfxt-add-junction.png)

幾秒之後，就會出現連接點。 請視需要建立額外的連接點。

創建交匯點後，用戶端使用命名空間路徑從存儲系統訪問檔。

## <a name="next-steps"></a>後續步驟

* [裝載 Avere vFXT 叢集](avere-vfxt-mount-clients.md)
* 瞭解[將資料移動到新 Blob 容器](avere-vfxt-data-ingest.md)的有效方法
