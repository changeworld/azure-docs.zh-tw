---
title: 取得以 Microsoft Azure 為基礎的 VM 映射的共用存取簽章 URI |Azure Marketplace
description: 說明如何取得 VM 映像的共用存取簽章 (SAS) URI。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 46b3764fe2a1e09875f2b92a461591b6ff08540f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147895"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>取得 VM 映像的共用存取簽章 URI

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將 Azure 虛擬機器供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 依照[取得 VM 映射的共用存取](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)簽章 URI 中的指示，管理您遷移的供應專案。

在發行過程中，您必須為每個與 SKU 相關聯的虛擬硬碟 (VHD) 提供統一資源識別項 (URI)。 Microsoft 需要在認證程序期間存取這些 VHD。 本文說明如何為每個 VHD 產生共用存取簽章 (SAS) URI。 請在 Cloud Partner 入口網站的 [SKU]**** 索引標籤中，輸入此 URI。

產生 VHD 的 SAS URI 時，請遵守下列需求：

- 僅支援非受控的 VHD。
- `List` 和 `Read`­ 權限不足。 *請勿*提供`Write` 或 `Delete` 存取。
- 存取期間 (*到期日*) 應為建立 SAS URI 起至少三週。
- 為了防止 UTC 時間變化，請將開始日期設定為目前日期的前一天。 例如，如果目前日期為 2014 年 10 月 6 日，則選取 10/5/2014。

## <a name="generate-the-sas-url"></a>產生 SAS URL

可以使用下列工具，透過兩種常見的方式產生 SAS URL：

- Microsoft 儲存體總管 - 適用於 Windows、macOS 和 Linux 的圖形化工具
- Microsoft Azure CLI - 建議用於非 Windows 作業系統和自動化或連續整合環境

### <a name="azure-cli"></a>Azure CLI

請使用下列步驟，透過 Azure CLI 來產生 SAS URI。

1. 下載並安裝 [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)。 版本可供 Windows、macOS 和 Linux 的各種發佈。
2. 建立 PowerShell 檔案 (`.ps1` 副檔名)、複製下列程式碼，然後將其儲存到本機。

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. 編輯檔案，以提供下列參數值。  應該以 UTC 日期時間格式提供日期，例如 `2016-10-25T00:00:00Z`。
   - `<account-name>` - Azure 儲存體帳戶名稱
   - `<account-key>` - Azure 儲存體帳戶金鑰
   - `<vhd-name>` - VHD 名稱
   - `<start-date>` - VHD 存取的權限開始日期。 提供目前日期前一天的日期。
   - `<expiry-date>` - VHD 存取的權限到期日。  提供至少超過目前日期三週的日期。

   下列範例顯示適當的參數值 (在撰寫本文時)。

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. 將變更儲存到此 PowerShell 指令碼。
5. 使用系統管理權限執行此指令碼，以產生適用於容器層級存取的 *SAS 連接字串*。  可以使用兩種基本方法：
   - 從主控台執行指令碼。  例如，在 Windows 中，在指令碼上按一下右鍵，然後選取**系統管理員身分執行**。
   - 使用管理權限從 PowerShell 指令碼編輯器 (例如 [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)) 執行指令碼。
     以下示範這個編輯器內所產生的 SAS 連接字串。

     ![在 PowerShell ISE 中產生 SAS URI](./media/publishvm_032.png)

6. 複製產生的 SAS 連接字串，並將其儲存到安全位置的文字檔。  請編輯此字串，以將關聯的 VHD 位置資訊新增到字串中，以建立最終的 SAS URI。
7. 在 Azure 入口網站中，瀏覽至內有與新產生 URI 相關聯之 VHD 的 blob 儲存體。
8. 複製 **Blob 服務端點**的 URL 值，如下所示。

    ![Azure 入口網站中的 Blob 服務端點](./media/publishvm_033.png)

9. 使用步驟 6 中的 SAS 連接字串編輯文字檔。  請使用下列格式建構完整的 SAS URI：

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    例如，如果 VDH 名稱為 `TestRGVM2.vhd`，則產生的 SAS URI 會是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

您計劃發佈之 SKU 中的每個 VHD 均需重複上述步驟。

### <a name="microsoft-storage-explorer"></a>Microsoft 儲存體總管

請使用下列步驟，以 Microsoft Azure 儲存體總管來產生 SAS URI。

1. 下載並安裝 [Microsoft Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。
2. 開啟 [Explorer]，並在左側的功能表列中，按一下 [新增帳戶]**** 圖示。  隨即顯示 [連線至 Azure 儲存體]**** 對話方塊。
3. 選取 [新增 Azure 帳戶]****，然後按一下 [登入]****。  繼續執行所需的步驟以登入 Azure 帳戶。
4. 在左側 [Explorer]**** 窗格中，瀏覽至您的 [儲存體帳戶]****，並展開此節點。
5. 以滑鼠右鍵按一下 VHD，然後從快顯功能表選取 [取得共用存取簽章]****。

    ![在 [Azure 總管] 中取得 SAS 項目](./media/publishvm_034.png)

6. 隨即顯示 [共用存取簽章]**** 對話方塊。 為下列欄位輸入值︰
   - **開始時間** - VHD 存取的權限開始時間。 提供目前日期前一天的日期。
   - **到期時間** - VHD 存取的權限到期日。  提供至少超過目前日期三週的日期。
   - **權限** - 選取 `Read` 和 `List` 權限。

     ![Azure 總管中的 SAS 對話方塊](./media/publishvm_035.png)

7. 按一下 [建立]****，以建立此 VHD 相關聯的 SAS URI。  對話方塊會立即顯示此作業的相關詳細資料。
8. 複製 **URL** 值，並將該值儲存到安全位置的文字檔中。

    ![在 [Azure 總管] 中建立 SAS URI](./media/publishvm_036.png)

    產生的 SAS URL 適用於容器層級存取。  若要使其明確，必須將相關聯的 VHD 名稱新增至其中。

9. 編輯文字檔。 在 SAS URL 中的 `vhds` 字串後面插入 VHD 名稱 (包含前置正斜線)。  最終的 SAS URI 格式應為：

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    例如，如果 VDH 名稱為 `TestRGVM2.vhd`，則產生的 SAS URI 會是：

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

您計劃發佈之 SKU 中的每個 VHD 均需重複上述步驟。

## <a name="verify-the-sas-uri"></a>驗證 SAS URI

請使用下列檢查清單，檢閱並驗證每個產生 SAS URI。  驗證：

- URI 的格式如下： `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` +`<sas-connection-string>`
- URI 包含您的 VHD 映像檔案名稱，包括檔案名稱的副檔名「.vhd」。
- 在 URI 的中間，出現 `sp=rl`。 此字串表示已指定 `Read` 和 `List` 存取權。
- 之後，也會出現 `sr=c`。 此字串表示已指定容器層級存取權。
- 複製 URI，並將其貼到瀏覽器，以開始下載相關的 Blob。  (您可以在下載完成之前取消作業。)

## <a name="next-steps"></a>後續步驟

如果您在產生 SAS URI 時遇到問題，請參閱 [SAS URL 常見問題](./cpp-common-sas-url-issues.md) (英文)。  否則，請將 SAS URI 儲存到安全的位置以供稍後使用。 需要此 URI，以在 Cloud Partner 入口網站中[發佈 VM 供應項目](./cpp-publish-offer.md) (英文)。
