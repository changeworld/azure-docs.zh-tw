---
title: 安裝 Azure FarmBeats
description: 本文介紹如何在 Azure 訂閱中安裝 Azure 伺服器場節拍
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479554"
---
# <a name="install-azure-farmbeats"></a>安裝 Azure FarmBeats

本文介紹如何在 Azure 訂閱中安裝 Azure 伺服器場節拍。

Azure FarmBeats 是可在 Azure Marketplace 中取得的企業對企業供應項目。 它支援跨供應商聚合農業資料集並生成可操作的見解。 Azure FarmBeats 通過使您能夠基於融合資料集構建人工智慧 （AI） 或機器學習 （ML） 模型來實現此。 Azure FarmBeats 的兩個主要元件是：

- **資料集器**：一個 API 層，用於跨不同提供程式對各種農業資料集進行聚合、正常化和上下文化。

- **加速器**：構建在 Datahub 之上的 Web 應用程式。 它啟動模型開發和視覺化。 加速器使用 Azure FarmBeats API 來演示引入感應器資料的視覺化作為圖表和模型輸出的視覺化作為地圖。

## <a name="general-information"></a>一般資訊

### <a name="components-installed"></a>已安裝元件

安裝 Azure 伺服器場Beats 時，Azure 訂閱中預配了以下資源：

| 已安裝 Azure 資源  | Azure 伺服器場節拍元件  |
|---------|---------|
| Application Insights   |      資料中心&加速器      |
| App Service 方案     |     資料中心&加速器     |
| App Service 方案   | 資料中心&加速器  |
| API 連線    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     資料中心&加速器      |
| Azure Batch 帳戶    | Datahub   |
| Azure 金鑰保存庫 |  資料中心&加速器        |
| Azure 地圖帳戶       |     加速器    |
| 事件中心命名空間    |     Datahub      |
| 邏輯應用程式      |  Datahub       |
| 儲存體帳戶      |     資料中心&加速器      |
| 時間序列深入解析     |    Datahub    |

### <a name="costs-incurred"></a>費用

Azure FarmBeats 的成本是基礎 Azure 服務成本的聚合。 可以使用[定價計算機](https://azure.microsoft.com/pricing/calculator)計算 Azure 服務的定價資訊。 總安裝的實際成本將因使用方式而異。 兩個元件的穩定狀態成本為：

- 資料集器 - 每天少於 10 美元
- 加速器 - 每天少於 2 美元

### <a name="regions-supported"></a>支援的區域

目前，Azure FarmBeats 在以下區域的公共雲環境中受支援：

- 澳大利亞東部
- 美國中部
- 美國東部
- 美國東部 2
- 美國西部
- 美國西部 2
- 北歐
- 西歐
- 東亞
- 東南亞

### <a name="time-taken"></a>所走的時間

Azure FarmBeats 的整個設置（包括準備和安裝）需要不到一個小時。

## <a name="prerequisites"></a>Prerequisites

在開始實際安裝 Azure FarmBeats 之前，您需要完成以下步驟：

### <a name="verify-permissions"></a>驗證許可權

安裝 Azure FarmBeats 需要 Azure 租戶中的以下許可權：

- 租戶 - AAD 應用程式建立者
- 訂閱 - 擁有者
- 正在其中安裝伺服器場Beats的資源組 - 擁有者

[創建 AAD 應用程式](#create-an-aad-application)步驟需要前兩個許可權。 如果需要，您可以讓具有相應許可權的人員創建 AAD 應用程式。

從市場運行 FarmBeats 安裝的人員必須是正在其中安裝 FarmBeats 的資源組的擁有者。 對於訂閱擁有者，在創建資源組時，將自動發生這種情況。 對於其他人，請預先創建資源組，並要求訂閱擁有者使您成為資源組的擁有者。

您可以按照[有關基於角色的存取控制](https://docs.microsoft.com/azure/role-based-access-control/check-access)的說明來驗證 Azure 門戶中的存取權限。

### <a name="decide-subscription-and-region"></a>決定訂閱和地區

您需要 Azure 訂閱 ID 和要安裝 Azure FarmBeats 的區域。 選擇"[區域支援"](#regions-supported)部分中列出的區域之一。

記下**Azure 訂閱 ID**和**Azure 區域**。

### <a name="create-an-aad-application"></a>創建 AAD 應用程式

Azure 伺服器場節拍需要 Azure 活動目錄應用程式創建和註冊。 要成功運行 AAD 創建腳本，需要以下許可權：

- 租戶 - AAD 應用程式建立者
- 訂閱 - 擁有者

使用 PowerShell 環境在雲殼實例中運行以下步驟。 將提示首次使用使用者選擇訂閱並創建存儲帳戶。 按照指示完成設置。

1. 下載[AAD 應用程式產生器腳本](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 預設情況下，該檔將下載到您的主目錄。 導航到目錄。

    ```azurepowershell-interactive
        cd
    ```

3. 運行 AAD 腳本

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. 該腳本要求提供以下三個輸入：

    - **FarmBeats 網站名稱**： 這是您的 FarmBeats Web 應用程式的唯一 URL 首碼。 如果已獲取首碼，則腳本將出錯。安裝後，您的 FarmBeats 部署將從HTTPs://\<FarmBeats 網站名稱>.azure 網站訪問，而搖曳的 API 將在 HTTPs://\<FarmBeats 網站名稱>api.azurewebsites.net

    - **Azure 登入識別碼**：為要添加為 FarmBeats 管理員的使用者提供 Azure 登入識別碼。 然後，此使用者可以向其他使用者授予訪問 FarmBeats Web 應用程式的許可權。 登入識別碼 通常是表單john.doe@domain.com中的 。 Azure UPN 也受支援。

    - **訂閱 ID**：這是要在其中安裝 Azure FarmBeats 的訂閱 ID

5. AAD 腳本大約需要 2 分鐘才能運行並輸出螢幕上的值以及同一目錄中的 json 檔。 如果您讓其他人運行該腳本，請讓他們與您共用此輸出。

### <a name="create-sentinel-account"></a>創建哨兵帳戶

Azure FarmBeats 設置使您能夠從歐洲航天局的[Sentinel-2](https://scihub.copernicus.eu/)衛星任務獲取衛星影像。 要配置此設置，您需要一個 Sentinel 帳戶。

按照以下步驟創建一個免費帳戶與哨兵：

1. 轉到官方[註冊](https://aka.ms/SentinelRegistration)頁面。
2. 提供所需的詳細資訊（名字、姓氏、使用者名、密碼和電子郵件 ID），並填寫表單。
3. 驗證連結將發送到已註冊的電子郵件 ID。 選擇電子郵件中提供的連結並完成驗證。

您的註冊過程已完成。 記下您的**哨兵使用者名和密碼****，一**旦驗證也完成。

## <a name="install"></a>安裝

現在，您已準備好安裝 FarmBeats。 按照以下步驟開始安裝：

1. 登入 Azure 入口網站。 在右上角選擇您的帳戶，然後切換到要安裝 Azure FarmBeats 的 Azure AD 租戶。

2. 轉到門戶中的 Azure 應用商店，並在應用商店中搜索**Azure FarmBeats。**

3. 將顯示一個具有 Azure FarmBeats 概述的新視窗。 選取 [建立]****。

4. 新視窗隨即出現。 通過選擇要安裝 Azure FarmBeats 的正確訂閱、資源組和位置來完成註冊過程。

5. 在 **"伺服器場節拍服務警報**"部分中提供應接收與 Azure FarmBeats 相關的任何服務警報的電子郵件地址。 在頁面底部選擇 **"下一步**"以移動到 **"依賴項"** 選項卡。

    ![基礎知識選項卡](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. 將單個條目從[AAD 腳本](#create-an-aad-application)的輸出複製到 AAD 應用部分中的輸入。

7. 在["哨兵帳戶"部分輸入"哨兵"帳戶](#create-sentinel-account)使用者名和密碼。 選擇 **"下一步**"以移動到"**審閱和創建**"選項卡。

    ![Dependencies Tab](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 驗證輸入的詳細資訊後，選擇 **"確定**"。 將顯示"使用條款"頁。 查看術語並選擇 **"創建**"以啟動安裝。 您將被重定向到可以跟蹤安裝進度的頁面。

安裝完成後，您可以通過導航到安裝期間提供的網站名稱來驗證安裝並開始使用 FarmBeats 門戶：HTTPs://\<FarmBeats 網站名稱>.azure 網站.net。 您應該會看到 FarmBeats 使用者介面，並帶有創建伺服器場的選項。

**資料庫**可以在HTTPs://\<FarmBeats 網站名稱>-api.azure 網站.net/wagger 中找到。 在這裡，您將看到不同的伺服器場節拍 API 物件，並在 API 上執行 REST 操作。

## <a name="upgrade"></a>升級

要將 FarmBeats 升級到最新版本，請使用 PowerShell 環境在雲殼實例中運行以下步驟。 使用者需要是安裝 FarmBeats 的訂閱的擁有者。

將提示首次使用使用者選擇訂閱並創建存儲帳戶。 按照指示完成設置。

1. 下載[升級腳本](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 預設情況下，該檔將下載到您的主目錄。 導航到目錄。

    ```azurepowershell-interactive
        cd
    ```

3. 運行升級腳本

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

輸入.json 檔的路徑是可選的。 如果未指定，腳本將要求所有必需的輸入。 升級應在大約 30 分鐘內完成。

## <a name="uninstall"></a>解除安裝

要卸載 Azure 伺服器場資料集或加速器，請完成以下步驟：

1. 登錄到 Azure 門戶並刪除安裝這些元件**的資源組**。

2. 轉到 Azure 活動目錄&刪除連結到 Azure FarmBeats 安裝的**Azure AD 應用程式**。

## <a name="next-steps"></a>後續步驟

您已經瞭解如何在 Azure 訂閱中安裝 Azure FarmBeats。 現在，瞭解如何[將使用者添加到](manage-users-in-azure-farmbeats.md#manage-users)Azure FarmBeats 實例。
