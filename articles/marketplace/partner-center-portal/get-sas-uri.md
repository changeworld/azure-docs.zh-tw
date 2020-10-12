---
title: 取得 VM 映射的 SAS URI-Azure Marketplace
description: 在 Azure Marketplace 中，為您的虛擬硬碟 (VHD) 產生共用存取簽章 (SAS) URI。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144485"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>取得 VM 映射的 SAS URI

在發佈過程中，您必須為每個與您的方案相關聯的 VHD (共用存取簽章) URI (先前稱為) 的 Sku。 Microsoft 需要在認證程序期間存取這些 VHD。 您將在合作夥伴中心的 [ **方案** ] 索引標籤上輸入這個 URI。

為您的 Vhd 產生 SAS Uri 有下列需求：

- 它們只支援非受控 Vhd。
- 只有 List 和 Read 許可權是必要的。 請勿提供寫入或刪除存取權。
- 存取期間 (到期日) 應為建立 SAS URI 起至少三週。
- 為了防止 UTC 時間變更，請將開始日期設定為目前日期的前一天。 例如，如果目前的日期為2020年6月16日，請選取 [6/15/2020]。

## <a name="generate-the-sas-address"></a>產生 SAS 位址

有兩個常用的工具可用來建立 SAS 位址 (URL)：

1. **Microsoft 儲存體總管** – 適用於 Windows、macOS 和 Linux 的圖形化工具。
2. **Microsoft Azure CLI** – 建議用於非 Windows 作業系統和自動化或持續整合環境。

### <a name="using-tool-1-microsoft-storage-explorer"></a>使用工具1： Microsoft 儲存體總管

1. 下載並安裝 [Microsoft Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。
2. 開啟 [explorer]，然後在左側功能表中選取 [ **新增帳戶**]。
3. 在 [連線 **到 Azure 儲存體** ] 對話方塊中，選取 [ **新增 azure 帳戶** ]，然後登入您的 azure 帳戶。
4. 在左側的 [儲存體帳戶] 窗格中，展開 [ **儲存體帳戶** ] 節點。
5. 以滑鼠右鍵按一下您的 VHD，然後選取 [ **取得共用存取**簽章]。
6. 在 [ **共用存取** 簽章] 對話方塊中，完成下欄欄位：

    1. 開始時間 – VHD 存取的權限開始日期。 提供目前日期前一天的日期。
    2. 到期時間 – VHD 存取的權限到期日。 提供至少超過目前日期三週的日期。
    3. 權限 – 選取 [讀取] 和 [列出] 權限。
    4. 容器層級 – 勾選 [產生容器層級共用存取簽章 URI] 核取方塊。

    ![共用存取簽章] 對話方塊。](media/vm/create-sas-uri-storage-explorer.png)

7. 若要建立此 VHD 相關聯的 SAS URI，請選取 [建立]。 對話方塊會重新整理並顯示此作業的相關詳細資料。

8. 複製 URI，並將其儲存到安全位置的文字檔中。

    ![正在複製 URI。](media/vm/create-sas-uri-shared-access-signature-details.png)

    產生的 SAS URI 適用於容器層級存取。 若要將它設為特定，請編輯文字檔以新增 VHD 名稱。

9. 在 SAS URI 中的 vhds 字串後面插入您的 VHD 名稱 (包含前置正斜線)。 最終 SAS URI 應如下：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. 針對您將發佈之方案中的每個 VHD 重複這些步驟。

### <a name="using-tool-2-azure-cli"></a>使用工具2： Azure CLI

1. 下載並安裝 [MICROSOFT AZURE CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I。 版本可供 Windows、macOS 和 Linux 的各種發佈。
2. 建立 PowerShell 檔案 (.ps1 副檔名)、複製下列程式碼，然後將其儲存到本機。

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. 編輯檔案，以使用下列參數值。 提供 UTC 日期時間格式的日期，例如 2020-04-01T00：00：00Z。

    - 帳戶名稱–您的 Azure 儲存體帳戶名稱。
    - 帳戶金鑰–您的 Azure 儲存體帳戶金鑰。
    - vhd-名稱–您的 VHD 名稱。
    - 開始日期– VHD 存取權的開始日期。 提供目前日期前一天的日期。
    - 到期日– VHD 存取的許可權到期日。 提供目前日期後至少三週的日期。

    以下是在撰寫) 時 (適當參數值的範例：

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. 儲存變更。
2. 使用下列其中一種方法，以系統管理權限執行此指令碼，以建立適用於容器層級存取的 SAS 連接字串：

    - 從主控台執行指令碼。 在 Windows 中，以滑鼠右鍵按一下指令碼，然後選取 [以系統管理員身分執行]。
    - 從 PowerShell 指令碼編輯器 (例如 [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)) 執行指令碼。 此畫面會顯示如何在此編輯器中建立 SAS 連接字串：

    [![在 PowerShell 編輯器中建立 SAS 連接字串](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. 複製 SAS 連接字串，並將其儲存到安全位置的文字檔。 請編輯此字串以新增 VHD 位置資訊，以建立最終的 SAS URI。
7. 在 Azure 入口網站中，移至包含與新 URI 相關聯之 VHD 的 blob 儲存體。
8. 複製 thebBlob 服務端點的 URL：

    ![正在複製 blob 服務端點的 URL。](media/vm/create-sas-uri-blob-endpoint.png)

9. 使用步驟 6 中的 SAS 連接字串編輯文字檔。 使用此格式建立完整的 SAS URI：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>驗證 SAS URI

請先檢查 SAS URI，再將它發佈到合作夥伴中心上，以避免在提交要求後與 SAS URI 相關的任何問題。 此程式是選擇性的，但建議使用。

- URI 包含您的 VHD 映射檔案名，包括副檔名 `.vhd` 。
- `Sp=rl` 出現在 URI 的中間附近。 這個字串會顯示指定的讀取和清單存取權。
- 當 `sr=c` 出現時，這表示已指定容器層級存取權。
- 複製 URI 並貼到瀏覽器以測試下載 blob (您可以在下載完成之前取消作業)。

## <a name="next-step"></a>後續步驟

- 請參閱 [建立 Azure 虛擬機器供應](azure-vm-create-offer.md)專案。
