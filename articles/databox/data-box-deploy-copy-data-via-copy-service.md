---
title: 教學課程：使用資料複製服務來複製到您的裝置
titleSuffix: Azure Data Box
description: 在本教學課程，您會了解如何透過資料複製服務將資料複製到 Azure 資料箱裝置
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 5b3db919056f24ad8b46c9925c044453e671d99f
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219152"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>教學課程：使用資料複製服務將資料複製到 Azure 資料箱 (預覽)

本教學課程說明如何使用資料複製服務擷取資料，而不需要中繼主機。 資料複製服務會在 Microsoft Azure 資料箱本機上執行、透過 SMB 連線到您的網路連接儲存裝置 (NAS) 裝置，以及將資料複製到資料箱。

使用資料複製服務：

- 在可能無法使用中繼主機的 NAS 環境中使用。
- 搭配需要數週時間來擷取和上傳資料的小型檔案。 資料複製服務可大幅改善小型檔案的擷取和上傳時間。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 將資料複製到資料箱

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您已完成本教學課程：[設定 Azure 資料箱](data-box-deploy-set-up.md)。
2. 您已收到資料箱裝置，且入口網站中的訂購狀態為 [已交付]。
3. 您有欲連線來源 NAS 裝置的認證可供資料複製使用。
4. 您已連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，則可以使用 1 GbE 資料連結，但是複製速度會受到影響。

## <a name="copy-data-to-data-box"></a>將資料複製到資料箱

在連線至 NAS 裝置後，下一個步驟是複製資料。 開始複製資料之前，請檢閱下列注意事項：

* 複製資料時，請確定資料大小符合 [Azure 儲存體和資料箱限制](data-box-limits.md)一文中所述的大小限制。

* 如果由資料箱上傳的資料同時由資料箱外部的其他應用程式上傳，則可能導致上傳作業失敗和資料損毀。

* 如果資料因為由資料複製服務讀取而有所修改，您可能會看到資料失敗或損毀。

> [!IMPORTANT]
> 在確認資料箱已將您的資料移轉至 Azure 儲存體之前，請務必先保留一份來源資料複本。

若要使用資料複製服務來複製資料，您需要建立一項作業：

1. 在資料箱裝置的本機 Web UI 中，移至 [管理] > [複製資料]。
2. 在 [複製資料] 頁面上，選取 [建立]。

    ![在 [複製資料] 頁面上選取 [建立]](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. 在 [設定作業並啟動] 對話方塊中，填寫下列欄位：
    
    |欄位                          |值    |
    |-------------------------------|---------|
    |**作業名稱**                       |作業的唯一名稱超過 230 個字元。 作業名稱中不允許以下字元：\<, \>、\|、\?、\*、\\、\:、\/ 和 \\\.         |
    |**來源位置**                |以下列格式提供資料來源的 SMB 路徑：`\\<ServerIPAddress>\<ShareName>` 或 `\\<ServerName>\<ShareName>`。        |
    |**使用者名稱**                       |用以存取資料來源的使用者名稱 (採用 `\\<DomainName><UserName>` 格式)。 如果本機系統管理員正在連線，他們需要明確的安全性權限。 以滑鼠右鍵按一下資料夾，選取 [屬性]，然後選取 [安全性]。 這樣應該會在 [安全性] 索引標籤中新增本機系統管理員。       |
    |**密碼**                       |用以存取資料來源的密碼。           |
    |**目的地儲存體帳戶**    |從清單中選取要作為資料上傳目的地的儲存體帳戶。         |
    |**目的地類型**       |從清單中選取目標儲存體類型：**區塊 Blob**、**分頁 Blob** 或 **Azure 檔案儲存體**。        |
    |**目的地容器/共用**    |輸入位於目的地儲存體帳戶中，要作為資料上傳目的地容器或共用的名稱。 此名稱可以是共用名稱或容器名稱。 例如，使用 `myshare` 或 `mycontainer`。 您也可以使用 `sharename\directory_name` 或 `containername\virtual_directory_name` 格式來輸入名稱。        |
    |**複製符合模式的檔案**    | 您可以利用下列兩種方式來輸入檔案名稱比對模式：<ul><li>**使用萬用字元運算式：** 萬用字元運算式僅支援 `*` 和 `?`。 例如，`*.vhd` 運算式會比對所有具有 `.vhd` 副檔名的檔案。 同樣地，`*.dl?` 會使用 `.dl` 副檔名或開頭為 `.dl` 的副檔名 (例如，`.dll`) 來比對所有檔案。 同樣地，`*foo` 會比對其名稱結尾為 `foo` 的所有檔案。<br>您可以在欄位中直接輸入萬用字元運算式。 根據預設，系統會將此欄位中的輸入值視為萬用字元運算式。</li><li>**使用規則運算式：** 支援 POSIX 型規則運算式。 例如，規則運算式 `.*\.vhd` 會比對具有 `.vhd` 副檔名的所有檔案。 針對規則運算式，直接提供 `<pattern>` 作為 `regex(<pattern>)`。 如需有關規則運算式的詳細資訊，請移至[規則運算式語言 - 快速參考](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)。</li><ul>|
    |**檔案最佳化**              |這項功能啟用時，系統會在擷取期間封裝小於 1 MB 的檔案。 此封裝動作可加快小型檔案的資料複製作業。 此外，當檔案數目遠遠超過目錄數目時，亦可省下不少時間。        |
 
4. 選取 [開始]。 系統會驗證輸入的內容，如果驗證成功，便會啟動作業。 系統可能需要花幾分鐘的時間才能讓作業啟動。

    ![從 [設定作業並啟動] 對話方塊啟動作業](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. 使用指定的設定來建立作業。 您可以暫停、繼續、取消或重新啟動作業。 選取作業名稱旁的核取方塊，然後選取適當按鈕。

    ![在 [複製資料] 頁面上管理作業](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - 如果作業在尖峰時段會影響 NAS 裝置的資源，您可以將其暫停：

        ![在 [複製資料] 頁面上暫停作業](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        稍後，您可以在非尖峰時段繼續作業：

        ![在 [複製資料] 頁面上繼續作業](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - 您可以隨時取消作業：

        ![在 [複製資料] 頁面上取消作業](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        取消作業時必須進行確認：

        ![確認作業取消](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        如果您決定要取消作業，已經複製的資料並不會遭到刪除。 若要刪除您已複製到資料箱裝置的資料，請重設裝置。

        ![重設裝置](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > 如果您取消或暫停作業，大型檔案可能只複製了一部分。 這些部分複製的檔案會以相同的狀態上傳至 Azure。 當您取消或暫停作業時，請確定檔案是否已徹底複製完成。 若要驗證檔案，請查看 SMB 共用或下載 BOM 檔案。

    - 如果作業因為暫時性錯誤 (例如網路故障) 而失敗，您可以重新啟動作業。 但如果作業已達到終端狀態 (例如，**成功**或**完成但發生錯誤**)，您就無法重新啟動作業。 作業失敗可能是檔案命名或檔案大小的問題所造成。 系統會記錄這些錯誤，但作業在完成後就無法重新啟動。

        ![重新啟動失敗的作業](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        如果您遇到失敗且無法重新啟動作業，請下載錯誤記錄並在記錄檔中查看該項失敗。 在更正問題後，請建立新的作業來複製檔案。 您也可以[透過 SMB 複製檔案](data-box-deploy-copy-data.md)。
    
    - 在這個版本中，您無法刪除作業。
    
    - 您可以建立的作業數量沒有限制，但一次最多只能平行執行 10 個作業。
    - 若已開啟**檔案最佳化**，系統便會在擷取時封裝小型檔案以改善複製效能。 在這些情況下，您會看到封裝的檔案 (其會有和檔案名稱一樣的 GUID)。 請勿刪除此檔案。 其會在上傳期間解除封裝。

6. 當作業正在進行時，在 [複製資料] 頁面上：

    - 在 [狀態] 資料行中，您可以檢視複製作業的狀態。 狀態可能是：
        - **執行中**
        - **已失敗**
        - **已成功**
        - **暫停中**
        - **已暫停**
        - **取消中**
        - **Canceled**
        - **已完成但發生錯誤**
    - 在 [檔案] 資料行中，您可以看到複製中檔案的數量和總體大小。
    - 在 [已處理] 資料行中，您可以看到已處理好檔案的數量和總體大小。
    - 在 [作業詳細資料] 資料行中，選取 [檢視] 即可查看作業詳細資料。
    - 如果複製過程中發生任何錯誤 (如 [錯誤數量] 資料行所示)，請移至 [錯誤記錄] 資料行並下載錯誤記錄，以進行疑難排解。

等待複製作業完成。 因為有些錯誤只會記錄在 [連線並複製] 頁面中，請先確定複製作業已完成且未出現任何錯誤，再進行下一個步驟。

![[連線並複製] 頁面上沒有錯誤](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

為了確保資料完整性，在複製資料時會計算內嵌的總和檢查碼。 在複製完成後，請選取 [檢視儀表板] 以確認裝置上已使用的空間和可用空間。
    
![確認儀表板上的可用空間和已使用的空間](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

在複製作業完成後，您可以選取 [準備寄送]。

>[!NOTE]
> 複製作業正在進行時，無法執行 [寄送準備]。

## <a name="next-steps"></a>後續步驟

請繼續進行下一個教學課程，了解如何將資料箱裝置寄回給 Microsoft。

> [!div class="nextstepaction"]
> [將 Azure 資料箱裝置寄給 Microsoft](./data-box-deploy-picked-up.md)
