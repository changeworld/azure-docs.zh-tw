---
title: 安裝 Azure FarmBeats
description: 本文說明如何在您的 Azure 訂用帳戶中安裝 Azure FarmBeats
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: b7d99c3bf61de17f9cebba834234cc8ea52f30d6
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131871"
---
# <a name="install-azure-farmbeats"></a>安裝 Azure FarmBeats

本文說明如何在您的 Azure 訂用帳戶中安裝 Azure FarmBeats。

Azure FarmBeats 是可在 Azure Marketplace 中取得的企業對企業供應項目。 它能夠在提供者之間匯總農業資料集，並產生可操作的深入解析。 Azure FarmBeats 會藉由讓您根據已融合的資料集建立人工智慧（AI）或機器學習（ML）模型，來執行這項操作。 Azure FarmBeats 的兩個主要元件如下：

- **Datahub**：可讓您跨不同提供者匯總、正規化和 coNtextualization 各種農業資料集的 API 層。

- **快速鍵**：以 Datahub 為基礎建立的 Web 應用程式。 它會快速開始您的模型開發和視覺效果。 此加速器會使用 Azure FarmBeats Api 來示範內嵌感應器資料的視覺效果，做為圖表和模型輸出作為地圖的視覺效果。

## <a name="general-information"></a>一般資訊

### <a name="components-installed"></a>已安裝元件

當您安裝 Azure FarmBeats 時，會在您的 Azure 訂用帳戶中布建下列資源：

| 已安裝 Azure 資源  | Azure FarmBeats 元件  |
|---------|---------|
| Application Insights   |      Datahub & 加速器      |
| App Service 方案     |     Datahub & 加速器     |
| App Service 方案   | Datahub & 加速器  |
| API 連線    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & 加速器      |
| Azure Batch 帳戶    | Datahub   |
| Azure 金鑰保存庫 |  Datahub & 加速器        |
| Azure 地圖服務帳戶       |     加速器    |
| 事件中樞命名空間    |     Datahub      |
| 邏輯應用程式      |  Datahub       |
| 儲存體帳戶      |     Datahub & 加速器      |
| 時間序列深入解析     |    Datahub    |

### <a name="costs-incurred"></a>產生的成本

Azure FarmBeats 的成本是基礎 Azure 服務成本的匯總。 您可以使用[定價計算機](https://azure.microsoft.com/pricing/calculator)來計算 Azure 服務的定價資訊。 總安裝的實際成本會根據使用量而有所不同。 這兩個元件的穩定狀態成本為：

- Datahub-每日小於 $10
- 快速鍵-每日小於 $2

### <a name="regions-supported"></a>支援的區域

目前，下欄區域中的公用雲端環境支援 Azure FarmBeats：

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

### <a name="time-taken"></a>花費時間

Azure FarmBeats 的整個設定（包括準備和安裝）將會花費不到一小時的時間。

## <a name="prerequisites"></a>Prerequisites

您必須先完成下列步驟，才能開始實際安裝 Azure FarmBeats：

### <a name="verify-permissions"></a>驗證許可權

您將需要 Azure 租使用者中的下列許可權，才能安裝 Azure FarmBeats：

- 租使用者-AAD 應用程式建立者
- 訂用帳戶-擁有者
- 要在其中安裝 FarmBeats 的資源群組-擁有者

[建立 AAD 應用程式](#create-an-aad-application)步驟所需的前兩個許可權。 如有需要，您可以取得具有適當許可權的人員，以建立 AAD 應用程式。 安裝 FarmBeats 的人員必須是要安裝 FarmBeats 之資源群組的擁有者。

您可以遵循[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/check-access)中的指示，確認 Azure 入口網站中的存取權限。

### <a name="decide-subscription-and-region"></a>決定訂用帳戶和區域

您將需要 Azure 訂用帳戶識別碼和您想要安裝 Azure FarmBeats 的區域。 選擇 [[支援的區域](#regions-supported)] 區段中所列的其中一個區域。

記下**Azure 訂**用帳戶識別碼和**azure 區域**。

### <a name="create-an-aad-application"></a>建立 AAD 應用程式

Azure FarmBeats 需要 Azure Active Directory 應用程式的建立和註冊。 若要成功執行 AAD 建立腳本，需要下列許可權-

- 租使用者-AAD 應用程式建立者
- 訂用帳戶-擁有者

在使用 PowerShell 環境的 Cloud Shell 實例中，執行下列步驟。 系統會提示第一次使用者選取訂用帳戶，並建立儲存體帳戶。 依照指示完成安裝。

1. 下載[AAD 應用程式](https://aka.ms/FarmBeatsAADScript)產生器腳本

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 根據預設，檔案會下載到您的主目錄。 流覽至目錄。

    ```azurepowershell-interactive
        cd
    ```

3. 執行 AAD 腳本

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. AAD 腳本需要大約2分鐘的時間來執行，並將畫面上的值以及相同目錄中的 json 檔案輸出。 如果您有其他人執行此腳本，請要求他們與您共用此輸出。

### <a name="create-sentinel-account"></a>建立 Sentinel 帳戶

您的 Azure FarmBeats 安裝程式可讓您從歐洲空間代理人的[Sentinel-2](https://scihub.copernicus.eu/)附屬任務為您的伺服器陣列取得衛星影像。 若要設定此設定，您需要 Sentinel 帳戶。

請遵循下列步驟來建立具有 Sentinel 的免費帳戶：

1. 前往官方[註冊](https://aka.ms/SentinelRegistration)頁面。
2. 提供必要的詳細資料（[名字]、[姓氏]、[使用者名稱]、[密碼] 和 [電子郵件識別碼]），並填寫表單。
3. 驗證連結會傳送至已註冊的電子郵件識別碼。 選取電子郵件中提供的連結，並完成驗證。

您的註冊程式已完成。 當驗證也完成之後，請記下您的**Sentinel 使用者名稱**和**sentinel 密碼**。

## <a name="install"></a>安裝

您現在已準備好安裝 FarmBeats。 請遵循下列步驟來開始安裝：

1. 登入 Azure 入口網站。 在右上角選取您的帳戶，並切換至您想要安裝 Azure FarmBeats 的 Azure AD 租使用者。

2. 移至入口網站中的 Azure Marketplace，並在 Marketplace 中搜尋**Azure FarmBeats** 。

3. 隨即會出現一個新視窗，其中顯示 Azure FarmBeats 的總覽。 選取 [建立]。

4. 新的視窗隨即出現。 選擇正確的訂用帳戶、資源群組，以及您想要安裝 Azure FarmBeats 的位置，以完成註冊程式。

5. 提供電子郵件地址，在 [ **FarmBeats 服務警示**] 區段中，應該會收到與 Azure FarmBeats 相關的任何服務警示。 選取頁面底部的 **[下一步**]，移至 [相依**性] 索引**標籤。

    ![[基本] 索引標籤](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. 將[aad 腳本](#create-an-aad-application)輸出中的個別專案複製到 aad 應用程式區段中的輸入。

7. 在 [Sentinel 帳戶] 區段中，輸入[sentinel 帳戶](#create-sentinel-account)的使用者名稱和密碼。 選取 **[下一步**] 以移至 [**審查 + 建立**] 索引標籤。

    ![Dependencies Tab](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 一旦輸入的詳細資料通過驗證，請選取 **[確定]** 。 [使用規定] 頁面隨即出現。 檢查條款，然後選取 [**建立**] 以開始安裝。 系統會將您重新導向至可以遵循安裝進度的頁面。

安裝完成之後，您可以藉由流覽至您在安裝期間提供的網站名稱，來驗證安裝並開始使用 FarmBeats portal： HTTPs://\<FarmBeats-網站名稱 >. azurewebsites. net。 您應該會看到 FarmBeats 使用者介面，並提供建立伺服器陣列的選項。

**Datahub**可以在 Https://\<FarmBeats-網站名稱 >-api. azurewebsites. net/swagger 中找到。 在這裡，您會看到不同的 FarmBeats API 物件，並在 Api 上執行 REST 作業。

## <a name="upgrade"></a>升級

若要將 FarmBeats 升級至最新版本，請使用 PowerShell 環境在 Cloud Shell 實例中執行下列步驟。 使用者必須是 FarmBeats 安裝所在之訂用帳戶的擁有者。

系統會提示第一次使用者選取訂用帳戶，並建立儲存體帳戶。 依照指示完成安裝。

1. 下載[升級腳本](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 根據預設，檔案會下載到您的主目錄。 流覽至目錄。

    ```azurepowershell-interactive
        cd
    ```

3. 執行升級腳本

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

輸入 json 檔案的路徑是選擇性的。 如果未指定，腳本會要求所有必要的輸入。 升級應該會在大約30分鐘後完成。

## <a name="uninstall"></a>解除安裝

若要卸載 Azure FarmBeats Datahub 或加速器，請完成下列步驟：

1. 登入 Azure 入口網站，並刪除已安裝這些元件**的資源群組**。

2. 移至 Azure Active Directory & 刪除連結至 Azure FarmBeats 安裝**的 Azure AD 應用程式**。

## <a name="next-steps"></a>後續步驟

您已瞭解如何在 Azure 訂用帳戶中安裝 Azure FarmBeats。 現在，請瞭解如何[將使用者新增](manage-users-in-azure-farmbeats.md#manage-users)至您的 Azure FarmBeats 實例。
