---
title: 取得 VM 映射的共用存取簽章 URI |Azure Marketplace
description: 本文說明如何取得每個虛擬硬碟（VHD）的共用存取簽章（SAS） URI。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732646"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>取得 VM 映像的共用存取簽章 URI

> [!IMPORTANT]
> 我們正在將您 Azure 虛擬機器供應專案的管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請依照[取得 VM 映射的共用存取](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)簽章 URI 中的指示進行 Cloud Partner 入口網站以管理您的供應專案。

本文說明如何產生每個虛擬硬碟（VHD）的共用存取簽章（SAS）統一資源識別元（URI）。

在發佈過程中，您必須為每個與您的方案相關聯的 VHD 提供 URI。 這些方案先前稱為 Sku 或庫存單位。 Microsoft 需要在認證程序期間存取這些 VHD。 您會在合作夥伴中心的 [**方案**] 索引標籤上輸入此 URI。

為您的 Vhd 產生 SAS Uri 時，請遵循下列需求：

* 僅支援非受控的 VHD。
* 只有`List`和`Read`許可權是必要的。 不提供寫入或刪除存取權。
* 存取期間 (到期日) 應為建立 SAS URI 起至少三週。
* 若要防止 UTC 時間變更，請將開始日期設定為目前日期之前的一天。 例如，如果目前的日期是2019年10月6日，請選取 [10/5/2019]。

## <a name="generate-the-sas-address"></a>產生 SAS 位址

有兩個常用的工具可用來建立 SAS 位址（URL）：

* **Microsoft 儲存體總管**–適用于 Windows、MacOS 和 Linux 的圖形化工具。
* **MICROSOFT AZURE CLI** –建議用於非 Windows 作業系統和自動化或連續整合環境。

### <a name="use-microsoft-storage-explorer"></a>使用 Microsoft 儲存體總管

1. 下載並安裝[Microsoft Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。
2. 開啟瀏覽器，然後在左側功能表中，選取 [**新增帳戶**]。 [**連接到 Azure 儲存體**] 對話方塊隨即出現。
3. 選取 [**新增 Azure 帳戶**]，然後登**入**。 完成登入您的 Azure 帳戶所需的步驟。
4. 在左側的 [**瀏覽器**] 窗格中，移至您的**儲存體帳戶**，然後展開此節點。
5. 以滑鼠右鍵按一下您的 VHD，然後選取 [**取得共用存取**簽章]。
6. [**共用存取**簽章] 對話方塊隨即出現。 完成以下欄位：

    * **開始時間**– VHD 存取的許可權開始日期。 提供目前日期前一天的日期。
    * **到期時間**– VHD 存取的許可權到期日。 提供至少三個星期超過目前日期的日期。
    * **許可權**–選取 [讀取] 和 [列出] 許可權。
    * **容器層級**–選取 [**產生容器層級共用存取**簽章 URI] 核取方塊。

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="說明 [共用存取簽章] 對話方塊":::

7. 若要建立此 VHD 的相關聯 SAS URI，請選取 [**建立**]。 對話方塊會重新整理並顯示此作業的詳細資料。
8. 複製**URI** ，並將它儲存到安全位置中的文字檔。

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="說明 [共用存取簽章詳細資料] 方塊":::

    這個產生的 SAS URI 適用于容器層級的存取。 若要將它設為特定，請編輯文字檔以新增 VHD 名稱（下一個步驟）。

9. 在 SAS URI 中的 vhd 字串後面插入 VHD 名稱（包括正斜線）。 最終的 SAS URI 看起來應該像這樣：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`例如，如果 VDH 的名稱為`TestRGVM2.vhd`，則產生的 SAS URI 會是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 針對您將發佈的方案中的每個 VHD 重複這些步驟。

### <a name="using-azure-cli"></a>使用 Azure CLI

1. 下載並安裝[MICROSOFT AZURE CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)。 版本可供 Windows、macOS 和 Linux 的各種發佈。
2. 建立 PowerShell 檔案（. ps1 副檔名），複製下列程式碼，然後將它儲存在本機。

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 編輯檔案以使用下列參數值。 以 UTC 日期時間格式提供日期，例如`2020-04-01T00:00:00Z`。

    * `<account-name>`–您的 Azure 儲存體帳戶名稱
    * `<account-key>`–您的 Azure 儲存體帳戶金鑰
    * `<vhd-name>`–您的 VHD 名稱
    * `<start-date>`– VHD 存取的許可權開始日期。 提供目前日期前一天的日期。
    * `<expiry-date>`– VHD 存取的許可權到期日。 提供目前日期後至少三周的日期。

    這個範例會顯示適當的參數值（在撰寫本文時）：

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 儲存變更。
5. 使用下列其中一種方法，以系統管理許可權執行此腳本，以建立容器層級存取的**SAS 連接字串**：

    * 從主控台執行指令碼。 在 Windows 中，以滑鼠右鍵按一下腳本，然後選取 [以**系統管理員身分執行**]。
    * 從 PowerShell 腳本編輯器（例如[Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)）執行腳本。 此畫面會顯示如何在此編輯器中建立 SAS 連接字串：

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="說明如何建立具有 Windows PowerShell ISE 的 SAS 連接字串":::

6. 複製 SAS 連接字串，並將它儲存到安全位置中的文字檔。 編輯此字串以新增 VHD 位置資訊，以建立最終的 SAS URI。
7. 在 Azure 入口網站中，移至包含與新 URI 相關聯之 VHD 的 blob 儲存體。
8. 複製**Blob 服務端點**的 URL，如下列螢幕擷取畫面所示

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="說明 Blob 服務端點":::

9. 使用步驟 6 中的 SAS 連接字串編輯文字檔。 使用下列格式建立完整的 SAS URI：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    例如，如果 VHD 的名稱是`TestRGVM2.vhd`，則 SAS URI 會是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

您計劃發佈之 SKU 中的每個 VHD 均需重複上述步驟。

## <a name="verify-the-sas-uri"></a>驗證 SAS URI

使用下列檢查清單來檢查每個建立的 SAS URI，以確認：

* URI 看起來像這樣：`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* 此 URI 包含您的 VHD 映射檔案名，包括檔案名副檔名 ".vhd"。
* `sp=rl`出現在 URI 的中間附近。 這個字串會顯示`Read`已`List`指定和存取。
* 當`sr=c`出現時，這表示會指定容器層級的存取權。
* 將 URI 複製並貼到瀏覽器以進行測試-下載 blob （您可以在下載完成之前取消作業）。

## <a name="next-step"></a>後續步驟

如果您在建立 SAS URI 時遇到困難，請參閱[常見的 SAS URL 問題](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)。 否則，請將 SAS URI 儲存到安全的位置以供稍後使用。 您將需要它，才能在合作夥伴中心發佈您的 VM 供應專案。

* [建立 Azure 虛擬機器供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
