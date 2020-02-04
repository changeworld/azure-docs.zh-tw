---
title: 安裝 Azure FarmBeats
description: 本文說明如何在您的 Azure 訂用帳戶中安裝 Azure FarmBeats
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482448"
---
# <a name="install-azure-farmbeats"></a>安裝 Azure FarmBeats

本文說明如何在您的 Azure 訂用帳戶中安裝 Azure FarmBeats。

Azure FarmBeats 是 Azure Marketplace 提供的企業對企業服務。 它能夠在提供者之間匯總農業資料集，並產生可操作的深入解析。 Azure FarmBeats 會藉由讓您根據已融合的資料集建立人工智慧（AI）或機器學習（ML）模型，來執行這項操作。 Azure FarmBeats 的兩個主要元件如下：

- **Datahub**：可讓您跨不同提供者匯總、正規化和 coNtextualization 各種農業資料集的 API 層。

- **快速鍵**：以 Datahub 為基礎建立的範例 web 應用程式。 它會快速開始您的模型開發和視覺效果。 此加速器會使用 Azure FarmBeats Api 來示範內嵌感應器資料的視覺效果，做為圖表和模型輸出作為地圖的視覺效果。

## <a name="before-you-start"></a>開始之前
### <a name="components-installed"></a>已安裝元件

當您安裝 Azure FarmBeats 時，會在您的 Azure 訂用帳戶中布建下列資源：

| 已安裝 Azure 資源  | Azure FarmBeats 元件  |
|---------|---------|
| Application Insights   |      Datahub & 加速器      |
| App Service     |     Datahub & 加速器     |
| App Service 方案   | Datahub & 加速器  |
| API 連線    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & 加速器      |
| Azure Batch 帳戶    | Datahub   |
| Azure Key Vault |  Datahub & 加速器        |
| Azure 地圖服務帳戶       |     加速器    |
| 事件中樞命名空間    |     Datahub      |
| Logic 應用程式      |  Datahub       |
| 儲存體帳戶      |     Datahub & 加速器      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>產生的成本

Azure FarmBeats 的成本是基礎 Azure 服務成本的匯總。 您可以使用[定價計算機](https://azure.microsoft.com/pricing/calculator)來計算 Azure 服務的定價資訊。 總安裝的實際成本會根據使用量而有所不同。 這兩個元件的穩定狀態成本為：

* Datahub-每日小於 $10
* 快速鍵-每日小於 $2

### <a name="regions-supported"></a>支援的區域

目前，下欄區域中的公用雲端環境支援 Azure FarmBeats：
* 澳大利亞東部
* 美國中部
* 美國東部
* 美國東部 2
* 美國西部
* 美國西部 2
* 北歐
* 歐洲西部
* 東南亞

### <a name="time-taken"></a>花費時間

Azure FarmBeats 的整個設定（包括準備和安裝）將會花費不到一小時的時間。

## <a name="prerequisites"></a>必要條件    

開始實際安裝 Azure FarmBeats 之前，您必須完成下列步驟：

### <a name="create-sentinel-account"></a>建立 Sentinel 帳戶
您的 Azure FarmBeats 設定可讓您從歐洲空間機構的[Sentinel-2](https://scihub.copernicus.eu/)附屬工作，取得您伺服器陣列的免費衛星影像。 若要設定此設定，您需要 Sentinel 帳戶。

請遵循下列步驟來建立具有 Sentinel 的免費帳戶：

1. 前往官方[註冊](https://scihub.copernicus.eu/dhus/#/self-registration)頁面。
2. 提供必要的詳細資料（[名字]、[姓氏]、[使用者名稱]、[密碼] 和 [電子郵件識別碼]），並填寫表單。
3. 驗證連結將會傳送至已註冊的電子郵件識別碼。 選取電子郵件中提供的連結，並完成驗證。

完成驗證之後，您的註冊程式就會完成。 記下 [Sentinel 使用者**名稱**] 和 [ **sentinel 密碼**]。

### <a name="decide-subscription-and-region"></a>決定訂用帳戶和區域

您將需要 Azure 訂用帳戶識別碼和您想要安裝 Azure FarmBeats 的區域。 選擇 [[支援的區域](#regions-supported)] 區段中所列的其中一個區域。

記下**Azure 訂**用帳戶識別碼和**azure 區域**。

### <a name="verify-permissions"></a>驗證許可權

您必須確認您想要安裝 Azure FarmBeats 的 Azure 租使用者中是否有足夠的許可權和許可權。

您可以遵循[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/check-access)上的指示，驗證 Azure 入口網站中的存取權限

若要安裝 Azure FarmBeats，您需要下列許可權：
- 租使用者-讀取權限
- 訂用帳戶-參與者或擁有者
- 資源群組-擁有者。

此外，Azure FarmBeats 需要 Azure Active Directory 的應用程式註冊。 有兩種方式可以完成必要的 Azure AD 設定：

**案例 1**：您在要安裝的 Azure 租使用者中具有**寫入**許可權。 這種情況表示您有必要的許可權，可以在安裝期間以動態方式建立 AAD 應用程式註冊。

您可以直接前往[完成 Marketplace 註冊](#complete-azure-marketplace-sign-up)一節。


**案例 2**：您在 Azure 租使用者中沒有**寫入**許可權。 當您嘗試在公司的 Azure 訂用帳戶中安裝 Azure FarmBeats，而且您的**寫入**許可權僅限於您擁有的資源群組時，這種情況很常見。
在此情況下，請要求您的 IT 系統管理員遵循下列步驟，以在 Azure 入口網站中自動產生並完成 Azure AD 應用程式註冊。

1. 下載[AAD 應用程式](https://aka.ms/FarmBeatsAADScript)產生器腳本，並將其解壓縮至您的本機電腦。
2. 登入 Azure 入口網站並選取您的訂用帳戶，然後 Azure AD 租使用者。
3. 從 Azure 入口網站頂端的工具列啟動 Cloud Shell。

    ![專案 FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. 選擇 PowerShell 作為慣用的 shell 體驗。 第一次，系統會提示使用者選取訂閱並建立儲存體帳戶。 依照指示完成安裝。
5. 將腳本（來自步驟1）上傳至 Cloud Shell，並記下上傳檔案的位置。

    > [!NOTE]
    > 根據預設，檔案會上傳到您的主目錄。

6. 使用 ' cd ' 命令移至主目錄，然後執行下列腳本：

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. 輸入 [ **Datahub 網站**名稱] 和 [**加速器網站**名稱]。 記下腳本的輸出，並與安裝 Azure FarmBeats 的人員共用。

一旦您的 IT 系統管理員為您提供必要的詳細資料，請記下  **Aad 用戶端識別碼、Aad 用戶端密碼、Datahub 網站名稱 & 快速鍵 網站名稱**。

   > [!NOTE]
   > 如果您遵循案例2的指示，請不要忘了將 AAD 用戶端識別碼 & AAD 用戶端密碼新增為[參數](#prepare-parameters-file)檔案中的個別參數

現在，您已擁有繼續下一節所需的所有資訊。

### <a name="complete-azure-marketplace-sign-up"></a>完成 Azure Marketplace 註冊

您必須先完成註冊 Azure marketplace 中的 Azure FarmBeats 供應專案，才能啟動以雲端 shell 為基礎的安裝程式。 請遵循下列步驟來完成註冊：

1.  登入 Azure 入口網站。 在右上角選取您的帳戶，並切換至您想要安裝 Azure FarmBeats 的 Azure AD 租使用者。

2.  移至入口網站中的 Azure Marketplace，並在 Marketplace 中搜尋**Azure FarmBeats**

3.  隨即會出現一個新視窗，其中顯示 Azure FarmBeats 的總覽。 選取 [建立]。

4.  新的視窗隨即出現。 選擇正確的訂用帳戶、資源群組，以及您想要安裝 Azure FarmBeats 的位置，以完成註冊程式。

5.  一旦輸入的詳細資料通過驗證，請選取 **[確定]** 。 [使用規定] 頁面隨即出現。 檢查條款，然後選取 [**建立**] 以完成註冊程式。

此步驟會完成 Azure Marketplace 中的註冊程式。 您現在已準備好開始準備參數檔案。

### <a name="prepare-parameters-file"></a>準備參數檔案
必要條件階段的最後一個步驟是建立 JSON 檔案，做為 Cloud Shell 安裝期間的輸入。 JSON 檔案中的參數必須以適當的值取代。

以下提供範例 JSON 檔案。 下載範例並更新所需的詳細資料。

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

您可以參考下列參數表，以瞭解每個參數的詳細資訊。

| 參數 | 值|
|--- | ---|
|sku  | 可讓使用者選擇同時安裝 Datahub 和加速器，或只是 Datahub。 若只要安裝 Datahub，請使用 "Datahub"。 若要安裝 Datahub 和加速器，請使用 "both"|
|subscriptionId | 指定要安裝 Azure FarmBeats 的 Azure 訂用帳戶|
|datahubResourceGroup| 指定 Datahub 資源的資源組名。 輸入您在這裡的 Azure Marketplace 中建立的資源組名|
|location |您想要安裝 Azure FarmBeats 的位置/Azure 區域|
|datahubWebsiteName  | Datahub web 應用程式的唯一 URL 前置詞 |
|acceleratorResourceGroup  | 指定加速器資源的資源組名|
|acceleratorWebsiteName |加速器 web 應用程式的唯一 URL 前置詞|
|sentinelUsername | 用來取得 Sentinel 附屬影像的使用者名稱|
|notificationEmailAddress  | 用來接收您在 Datahub 內設定之任何警示通知的電子郵件地址|
|updateIfExists| 選擇性只有在您想要升級現有的 Azure FarmBeats 實例時，才會包含在參數檔案中的參數。 若要進行升級，其他詳細資料（例如資源組名和位置）必須相同|
|aadAppClientId | 選擇性只有在您使用預先建立的 AAD 應用程式時，才會包含在參數檔案中的參數。 如需詳細資訊，請參閱[驗證許可權](#verify-permissions)一節中的案例2 |
|aadAppClientSecret  | 選擇性選擇性只有在您使用預先建立的 AAD 應用程式時，才會包含在參數檔案中的參數。 如需詳細資訊，請參閱[驗證許可權](#verify-permissions)一節中的案例2|

根據上表和範例 JSON 檔案，建立您的參數 JSON 檔案，並將它儲存在您的本機電腦中。

## <a name="install"></a>安裝

實際安裝的 Azure FarmBeats 資源會在以瀏覽器為基礎的命令列介面中，使用 Bash 環境進行 Cloud Shell。 請依照下列指示來安裝 Azure FarmBeats：

1. 登入 Azure 入口網站。 選取您想要安裝 Azure FarmBeats 的 Azure 訂用帳戶和租使用者。
2. 從 Azure 入口網站右上角的工具列啟動**Cloud Shell** 。
3. 選擇 Bash 做為慣用的 shell 體驗。 選取 [**上傳**] 按鈕（在下圖中反白顯示），然後上傳已備妥的參數 JSON 檔案。

      ![專案 FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. **複製**下列命令，並以正確的值**取代 \<username >** ，讓命令指向所上傳檔案的正確路徑。

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. 執行修改過的命令以開始安裝程式。 系統會提示您：
 - 同意**Azure FarmBeats 授權**條款。 如果您同意使用規定，請輸入 "Y" 以繼續進行下一個步驟。 如果您不同意使用規定，請輸入 "N" 以結束安裝。

 - 接下來，系統會提示您輸入安裝的存取權杖。 複製產生的程式碼，並使用您的**Azure 認證**登入[裝置登入頁面](https://microsoft.com/devicelogin)。

 - 登入成功完成後，安裝程式將會提示您輸入 Sentinel 帳戶密碼。 輸入您的**Sentinel 帳戶密碼**。

6. 系統會驗證參數檔案，並開始安裝 Azure 資源。 安裝大約需要**25 分鐘的時間**才能完成。    
> [!NOTE]
> 非使用中 Cloud Shell 會話會在**20 分鐘**後到期。 當安裝程式正在部署 Azure 資源時，請將 Cloud Shell 會話保持在使用中狀態。 如果會話超時，您就必須重新開機安裝程式。

安裝完成之後，您將會收到下列輸出連結：
* **DATAHUB URL**：用於存取 Datahub Api 的 Swagger 連結。
* **快速鍵 URL**：用於探索 Azure FarmBeats 加速器的 web 應用程式。
* **安裝程式記錄**檔：包含安裝詳細資料的記錄檔。 如有必要，可以使用此記錄檔來進行安裝的疑難排解。

您可以執行下列檢查，確認您的 Azure FarmBeats 安裝完成：

**Datahub**
1. 使用您的 Azure 認證登入所提供的快速鍵 URL （格式為**HTTPs://\<yourdatahub-網站名稱 >. azurewebsites. net/swagger**）。
2. 您應該可以看到不同的 FarmBeats API 物件，並在 Api 上執行 REST 作業。

**加速器**
1. 使用您的 Azure 認證登入所提供的快速鍵 URL （格式為**HTTPs://\<youraccelerator-網站名稱 >. azurewebsites. net/swagger**）。
2. 您應該可以看到加速器使用者介面，並提供在瀏覽器中建立伺服器陣列的選項。

您執行上述作業的能力表示已成功安裝 Azure FarmBeats。

## <a name="upgrade"></a>升級
在公開預覽版本中，若要升級現有的 Azure FarmBeats 安裝，您必須在 Cloud Shell 中再次執行安裝命令，並在 Parameters 檔案中將額外的 "**updateIfExists**" 參數設定為 "**true**"。 請參閱下列 JSON 範例的最後一行，以取得 update 參數。

```json
{
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```
此命令會將現有的 Azure FarmBeats 安裝更新為最新版本，並為您提供輸出連結。

## <a name="uninstall"></a>解除安裝

若要卸載 Azure FarmBeats Datahub 或加速器，請完成下列步驟：

1.  登入 Azure 入口網站，並刪除已安裝這些元件**的資源群組**。

2.  移至 Azure Active Directory & 刪除連結至 Azure FarmBeats 安裝**的 Azure AD 應用程式**。 這會從您的 Azure 訂用帳戶中移除 Azure FarmBeats 安裝。

## <a name="next-steps"></a>後續步驟
您已瞭解如何在 Azure 訂用帳戶中安裝 Azure FarmBeats。 現在，請瞭解如何[將使用者新增](manage-users-in-azure-farmbeats.md#manage-users)至您的 Azure FarmBeats 實例。
