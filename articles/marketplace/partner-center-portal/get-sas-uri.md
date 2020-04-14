---
title: 取得 VM 映像的共享存取簽章 URI |Azure 應用商店
description: 本文介紹如何獲取每個虛擬硬碟 (VHD) 的共享訪問簽名 (SAS) URI。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: 50bda733b0bb8b0c98eb69a15ab3000ad278031c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265703"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>取得 VM 映像的共用存取簽章 URI

> [!IMPORTANT]
> 我們將 Azure 虛擬機器產品的管理從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照雲合作夥伴門戶[的 VM 映射獲取共享訪問簽名 URI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)中的說明來管理您的產品/服務。

本文介紹如何為每個虛擬硬碟 (VHD) 生成共享訪問簽名 (SAS) 統一資源標識符 (URI)。

在發佈過程中,您必須為與計畫關聯的每個 VHD 提供 URI。 這些計劃以前稱為 SKU 或庫存單位。 Microsoft 需要在認證程序期間存取這些 VHD。 您將在合作夥伴中心的 **「計劃」** 選項卡上輸入此 URI。

為 VHD 生成 SAS URI 時,請遵循以下要求:

* 僅支援非受控的 VHD。
* 僅`List`需要`Read`許可權和許可權。 不提供寫入或刪除訪問許可權。
* 存取期間 (到期日) 應為建立 SAS URI 起至少三週。
* 要防止 UTC 時間更改,將開始日期設置為當前日期之前的一天。 例如,如果當前日期為 2019 年 10 月 6 日,請選擇 2019 年 10 月 5 日。

## <a name="generate-the-sas-address"></a>產生 SAS 位址

有兩種常用工具用於建立 SAS 位址 (URL):

* **微軟儲存資源管理員**– 可用於 Windows、macOS 和 Linux 的圖像工具。
* **Microsoft Azure CLI** – 推薦用於非 Windows 操作系統和自動或連續整合環境。

### <a name="use-microsoft-storage-explorer"></a>使用微軟儲存資源管理員

1. 下載並安裝[Microsoft Azure 資源管理員](https://azure.microsoft.com/features/storage-explorer/)。
2. 開啟資源管理員,並在左側功能表中選擇「**添加帳戶**」。 將顯示「**連接到 Azure 儲存**」對話框。
3. 選擇 **'新增 Azure 帳號**',然後**登入**。 完成登錄 Azure 帳戶所需的步驟。
4. 在左側**資源管理員**窗格中,轉到**存儲帳戶**並展開此節點。
5. 右鍵按一下 VHD,然後選擇 **「獲取共享存取簽名**」。
6. 將顯示「**共享存取簽名**」 對話框。 完成以下欄位：

    * **開始時間**= VHD 存取的許可權開始日期。 提供目前日期前一天的日期。
    * **過期時間**= VHD 存取的許可權到期日期。 提供比當前日期至少三周的日期。
    * **權限**= 選擇「讀取」 和「列表」 許可權。
    * **容器級**= 選擇**中 「生成容器級共用存取簽章 URI」** 複選方塊。

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="說明共享存取簽名對話框":::

7. 要為此 VHD 創建關聯的 SAS URI,請選擇 **"創建**"。 對話框將刷新並顯示有關此操作的詳細資訊。
8. 複製**URI**並將其保存到安全位置的文本檔中。

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="說明共享存取簽署詳細資訊框":::

    此生成的 SAS URI 用於容器級訪問。 要使其具體化,請編輯文本檔以添加 VHD 名稱(下一步)。

9. 在 SAS URI 中的 vhds 字串之後插入 VHD 名稱(包括正向斜杠)。 最終的 SAS URI 應如下所示:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`例如,如果 VDH 的`TestRGVM2.vhd`名稱為 ,則產生的 SAS URI 將是:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 對要發佈的計劃中的每個 VHD 重複這些步驟。

### <a name="using-azure-cli"></a>使用 Azure CLI

1. 下載並安裝[微軟 Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)。 版本可供 Windows、macOS 和 Linux 的各種發佈。
2. 創建 PowerShell 檔(.ps1 檔案副檔名),在以下代碼複製,然後將其儲存在本地。

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 編輯檔案以使用以下參數值。 以 UTC 日期時間格式`2020-04-01T00:00:00Z`(如 )提供日期。

    * `<account-name>`• Azure 儲存帳戶名稱
    * `<account-key>`• Azure 儲存帳戶金鑰
    * `<vhd-name>`• 您的 VHD 名稱
    * `<start-date>`• VHD 訪問的許可權開始日期。 提供目前日期前一天的日期。
    * `<expiry-date>`• VHD 訪問的許可權到期日期。 在當前日期後至少三周提供日期。

    此範例顯示正確的參數值(在編寫本文時):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 儲存變更。
5. 使用以下方法之一,使用管理權限執行此文稿,為容器級存取建立**SAS 連接字串**:

    * 從主控台執行指令碼。 在 Windows 中,右鍵單擊腳本並選擇「**以管理員身份運行**」 。
    * 從 PowerShell 文稿編輯器(如[Windows PowerShell ISE)](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)執行文稿。 此螢幕顯示在此編輯器中建立 SAS 連接字串:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="展示使用 Windows PowerShell ISE 建立 SAS 連接字串":::

6. 複製 SAS 連接字串並將其儲存到安全位置的文本檔中。 編輯此字串以添加 VHD 位置資訊以建立最終的 SAS URI。
7. 在 Azure 門戶中,轉到包含與新 URI 關聯的 VHD 的 Blob 儲存。
8. 複製**Blob 服務終結點**的網址,如以下螢幕擷取所示

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="說明 Blob 服務終結點":::

9. 使用步驟 6 中的 SAS 連接字串編輯文字檔。 使用此格式建立完整的 SAS URI:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    例如,如果 VHD 的`TestRGVM2.vhd`名稱為 ,則 SAS URI 將為:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

您計劃發佈之 SKU 中的每個 VHD 均需重複上述步驟。

## <a name="verify-the-sas-uri"></a>驗證 SAS URI

使用以下檢查表檢查每個建立的 SAS URI,以驗證:

* URI 如下所示:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI 包括 VHD 映像檔名,包括檔名擴展名".vhd"。
* `sp=rl`顯示在URI的中間附近。 此字串顯示`Read`該`List`和 訪問已指定。
* 出現時`sr=c`,這意味著指定了容器級訪問。
* 將 URI 複製並貼上到瀏覽器中以測試下載 Blob(您可以在下載完成之前取消操作)。

## <a name="next-step"></a>後續步驟

如果您在建立 SAS URI 時遇到困難,請參閱[常見的 SAS URL 問題](https://aka.ms/AzureSAS_URI_FAQ)。 否則，請將 SAS URI 儲存到安全的位置以供稍後使用。 您需要它在合作夥伴中心發佈 VM 產品/服務。

* [建立 Azure 虛擬機器產品/服務](https://aka.ms/Create_AzureVMoffer)
