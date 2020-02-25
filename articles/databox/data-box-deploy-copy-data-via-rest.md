---
title: 教學課程：使用 REST API 複製到 Blob 儲存體
titleSuffix: Azure Data Box
description: 了解如何透過 REST API 將資料複製到您的 Azure 資料箱 Blob 儲存體
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: alkohli
ms.openlocfilehash: b7d58bb13644c992894510f26a4848ea80c9df00
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471834"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>教學課程：透過 REST API 將資料複製到 Azure 資料箱 Blob 儲存體  

本教學課程說明透過 REST API (透過 *http* 或 *https*) 連線到 Azure 資料箱 Blob 儲存體的程序。 連線之後，也會說明將資料複製到資料箱 Blob 儲存體並準備寄送資料箱所需的步驟。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * Prerequisites
> * 透過 *http* 或 *https* 連線到資料箱 Blob 儲存體
> * 將資料複製到資料箱

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已完成[教學課程：設定 Azure 資料箱](data-box-deploy-set-up.md)。
2. 您已收到資料箱，且入口網站中的訂購狀態為 [已交付]  。
3. 您已檢閱過[資料箱 Blob 儲存體的系統需求](data-box-system-requirements-rest.md)，並熟悉 API、SDK 和工具的支援版本。
4. 您有權存取包含要複製到資料箱之資料的主機電腦。 您的主機電腦必須符合下列條件：
    - 執行[支援的作業系統](data-box-system-requirements.md)。
    - 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10-GbE 連線，可以使用 1-GbE 資料連結，但是複製速度將會受到影響。
5. 在您的主機電腦上[下載 AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417)。 您將會使用 AzCopy，將資料從主機電腦複製到 Azure 資料箱 Blob 儲存體。


## <a name="connect-via-http-or-https"></a>透過 http 或 https 來連線

您可以透過 *http* 或 *https* 連線到資料箱 Blob 儲存體。

- https  是連線到資料箱 Blob 儲存體的安全建議方式。
- 透過受信任的網路連線時，會使用 *http*。

透過 *http* 或 *https* 連線至資料箱 Blob 時，兩者的連線步驟是不同的。

## <a name="connect-via-http"></a>透過 HTTP 連線

透過 *http* 連線到資料箱 Blob 儲存體 REST API 需要下列步驟：

- 將裝置 IP 和 Blob 服務端點新增到遠端主機
- 設定協力廠商軟體並驗證連線

下列各節將說明這其中每個步驟。

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>新增裝置 IP 位址和 Blob 服務端點

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="configure-partner-software-and-verify-connection"></a>設定合作夥伴軟體並驗證連線

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>透過 HTTPS 連線

透過 https 連線到 Azure Blob 儲存體 REST API 需要下列步驟：

- 從 Azure 入口網站下載憑證
- 匯入用戶端或遠端主機上的憑證
- 將裝置 IP 和 Blob 服務端點新增到用戶端或遠端主機
- 設定協力廠商軟體並驗證連線

下列各節將說明這其中每個步驟。

### <a name="download-certificate"></a>下載憑證

使用 Azure 入口網站下載憑證。

1. 登入 Azure 入口網站。
2. 移至您的資料箱訂單，並巡覽至 [一般] > [裝置詳細資料]  。
3. 在 [裝置認證]  下方，移至 [裝置的 API 存取]  。 按一下 [下載]  。 此動作會下載 **\<您的訂單號碼>.cer** 憑證檔案。 **儲存**這個檔案。 您會將此憑證安裝在將用來連線到裝置的用戶端或主機電腦上。

    ![在 Azure 入口網站下載憑證](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>匯入憑證 

必須有裝置的 SSL 憑證，才能透過 HTTPS 存取資料箱 Blob 儲存體。 讓用戶端應用程式可以使用此憑證的方式，會隨著應用程式和作業系統與散發套件而有所不同。 某些應用程式可以存取已匯入到系統憑證存放區中的憑證，某些應用程式則無法利用該機制。

本節會說明某些應用程式的特定資訊。 如需其他應用程式的詳細資訊，請參閱所使用應用程式和作業系統的文件。

請遵循下列步驟來將 `.cer` 檔案匯入到 Windows 或 Linux 用戶端的根存放區。 在 Windows 系統上，您可以使用 Windows PowerShell 或 Windows Server UI 在系統上匯入並安裝憑證。

#### <a name="use-windows-powershell"></a>使用 Windows PowerShell

1. 以系統管理員的身分開啟 Windows PowerShell工作階段。
2. 在命令提示字元中，輸入：

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>使用 Windows Server UI

1.  以滑鼠右鍵按一下 `.cer` 檔案，然後選取 [安裝憑證]  。 這個動作會啟動 [憑證匯入精靈]。
2.  [存放區位置]  請選取 [本機電腦]  ，然後按一下 [下一步]  。

    ![使用 PowerShell 匯入憑證](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  選取 [將所有憑證放入以下的存放區]  ，然後按一下 [瀏覽]  。 導覽至遠端主機的根存放區，然後按一下 [ **下一步**]。

    ![使用 PowerShell 匯入憑證](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  按一下 [完成]  。 會出現訊息告訴您匯入成功。

    ![使用 PowerShell 匯入憑證](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>使用 Linux 系統

用來匯入憑證的方法隨散發套件而異。

Ubuntu 和 Debian 等數個散發套件使用 `update-ca-certificates` 命令。  

- 重新命名 Base64 編碼的憑證檔案，使其具有 `.crt` 副檔名，然後將其複製到 `/usr/local/share/ca-certificates directory`。
- 執行命令 `update-ca-certificates`。

RHEL、Fedora 及 CentOS 的新近版本則使用 `update-ca-trust` 命令。

- 將憑證檔案複製到 `/etc/pki/ca-trust/source/anchors` 目錄。
- 執行 `update-ca-trust`。

如需詳細資訊，請參閱散發套件的專屬文件。

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>新增裝置 IP 位址和 Blob 服務端點 

請遵循相同的步驟，以[在透過 http  連線時新增裝置的 IP 位址和 Blob 服務端點](#add-device-ip-address-and-blob-service-endpoint)。

### <a name="configure-partner-software-and-verify-connection"></a>設定合作夥伴軟體並驗證連線

請遵循相關步驟，以[在透過 http  連線時設定所使用的合作夥伴軟體](#configure-partner-software-and-verify-connection)。 唯一的差別是您應該使 [使用 http 選項]  保持未核取狀態。

## <a name="copy-data-to-data-box"></a>將資料複製到資料箱

連線到資料箱 Blob 儲存體之後，下一個步驟是複製資料。 複製資料之前，請檢閱下列注意事項：

-  複製資料時，請確定資料大小符合 [Azure 儲存體和資料箱限制](data-box-limits.md)中所述的大小限制。
- 如果資料 (由資料箱上傳) 同時會由資料箱以外的其他應用程式上傳，這可能導致上傳作業失敗且資料損毀。

在本教學課程中，會使用 AzCopy 來將資料複製到資料箱 Blob 儲存體。 您也可以使用 Azure 儲存體總管 (如果您偏好 GUI 型工具) 或合作夥伴軟體來複製資料。

複製程序的步驟如下：

- 建立容器
- 將資料夾的內容上傳到資料箱 Blob 儲存體
- 將修改過的檔案上傳到資料箱 Blob 儲存體

下列各節將詳細討論這其中每個步驟。

### <a name="create-a-container"></a>建立容器

第一個步驟是建立容器，因為一律會將 Blob 上傳到容器。 容器會組織 Blob 群組，就像您在電腦上的資料夾中組織檔案一樣。 請遵循下列步驟來建立 Blob 容器。

1. 開啟儲存體總管。
2. 在左窗格中，展開您要在其中建立 blob 容器的儲存體帳戶。
3. 以滑鼠右鍵按一下 [Blob 容器]  ，從捷徑功能表中，選取 [建立 Blob 容器]  。

   ![建立 Blob 容器內容功能表](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. [Blob 容器]  資料夾下方會出現一個文字方塊。 輸入 blob 容器的名稱。 請參閱[建立容器與設定權限](../storage/blobs/storage-quickstart-blobs-dotnet.md)，以取得為 Blob 容器命名之規則和限制的相關資訊。
5. 完成建立 blob 容器時按下 **Enter**鍵，或按下 **Esc** 鍵取消。 成功建立 Blob 容器之後，它就會顯示在所選儲存體帳戶的 [Blob 容器]  資料夾下方。

   ![Blob 容器已建立](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>將資料夾的內容上傳到資料箱 Blob 儲存體

使用 AzCopy，將資料夾中的所有檔案都上傳到 Windows 或 Linux 上的 Blob 儲存體。 若要上傳資料夾中的所有 blob，請輸入下列 AzCopy 命令：

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


使用您的帳戶金鑰來取代 `<key>`。 若要取得帳戶金鑰，在 Azure 入口網站中，移至您的儲存體帳戶。 移至 [設定] > [存取金鑰]  、選取金鑰，並將它貼到 AzCopy 命令中。

如果指定的目的地容器不存在，則 AzCopy 會建立此容器並將檔案上傳至該容器中。 將來源路徑更新為您的資料目錄，並使用與您資料箱相關聯的儲存體帳戶名稱來取代目的地 URL 中的 `data-box-storage-account-name`。

若要以遞迴方式將指定目錄的內容上傳到 Blob 儲存體，請指定 `--recursive` (Linux) 或 `/S` (Windows) 選項。 當您使用其中一個選項執行 AzCopy 時，所有的子資料夾和其檔案也都會上傳。

### <a name="upload-modified-files-to-data-box-blob-storage"></a>將修改過的檔案上傳到資料箱 Blob 儲存體

使用 AzCopy，根據檔案的上次修改時間來上傳檔案。 若要這麼做，請基於測試目的，在您的來源目錄中修改或建立新的檔案。 若只要上傳已更新或新的檔案，請將 `--exclude-older` (Linux) 或 `/XO` (Windows) 參數新增至 AzCopy 命令。

如果您只想複製目的地中不存在的來源資源，請在 AzCopy 命令中同時指定 `--exclude-older` 和 `--exclude-newer` (Linux) 或 `/XO` 和 `/XN` (Windows) 參數。 AzCopy 只會上傳已更新的資料 (根據其時間戳記)。

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

如果在連線或複製作業期間發生任何錯誤，請參閱[針對資料箱 Blob 儲存體問題進行疑難排解](data-box-troubleshoot-rest.md)。

下一個步驟是準備寄送裝置。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * Prerequisites
> * 透過 *http* 或 *https* 連線到資料箱 Blob 儲存體
> * 將資料複製到資料箱


請繼續進行下一個教學課程，了解如何將資料箱送回給 Microsoft。

> [!div class="nextstepaction"]
> [將您的 Azure 資料箱寄送給 Microsoft](./data-box-deploy-picked-up.md)
