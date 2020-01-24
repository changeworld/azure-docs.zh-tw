---
title: 從合作夥伴取得感應器資料
description: 本文說明如何從合作夥伴取得感應器資料。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 9bf5608a44aa19650a507ada3a0a437d34c13277
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705661"
---
# <a name="get-sensor-data-from-sensor-partners"></a>從感應器合作夥伴取得感應器資料

Azure FarmBeats 可協助您將 IoT 裝置和感應器的串流資料帶入 Datahub。 目前支援下列感應器裝置合作夥伴。

  ![FarmBeats 合作夥伴](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

將裝置資料與 Azure FarmBeats 整合，可協助您從伺服器陣列中部署的 IoT 感應器，取得資料中樞的基礎資料。 資料一經提供，就可以透過 FarmBeats 加速器來視覺化。 資料可以用於資料融合，並使用 FarmBeats 來建立機器學習/人工智慧（ML/AI）模型。

若要啟動感應器資料串流，請確定下列事項：

-  您已在 Azure Marketplace 中安裝 FarmBeats。
-  您決定要在伺服器陣列上安裝的感應器和裝置。
-  如果您打算使用土裡濕度感應器，請使用 FarmBeats 的土濕度感應器放置圖來取得感應器數目的建議，以及您應該將它們放在何處。 如需詳細資訊，請參閱[產生對應](generate-maps-in-azure-farmbeats.md)。
- 您可以從您的伺服器陣列上的裝置夥伴購買並部署裝置或感應器。 請確定您可以透過裝置合作夥伴的解決方案存取感應器資料。

## <a name="enable-device-integration-with-farmbeats"></a>啟用裝置與 FarmBeats 的整合

開始串流處理感應器資料之後，您就可以開始將資料放入 FarmBeats 系統的程式。 將下列資訊提供給您的裝置提供者，以啟用與 FarmBeats 的整合：

 - API 端點
 - 租用戶識別碼
 - 用戶端識別碼
 - 用戶端密碼
 - EventHub 連接字串

您可以遵循下列步驟來產生上述資訊：（請注意，您必須在 Azure 上執行這些步驟，才能存取部署 FarmBeats 的 Azure 訂用帳戶）

1. 下載[zip](https://aka.ms/farmbeatspartnerscriptv2)檔案，並將它解壓縮到您的本機磁片磁碟機。 Zip 檔案中將會有一個檔案。
2. 登入 https://portal.azure.com/ 並移至 Azure Active Directory > 應用程式註冊

3. 按一下在 FarmBeats 部署過程中建立的應用程式註冊。 它的名稱會與您的 FarmBeats 資料中樞相同。

4. 按一下 [公開 API]-> 按一下 [新增用戶端應用程式] 並輸入**04b07795-8ddb-461a-bbee-02f9e1bf7b46** ，然後選取 [授權範圍]。 這會授與 Azure CLI （Cloud Shell）的存取權，以執行下列步驟。

5. 開啟 Cloud Shell。 此選項可在 Azure 入口網站右上角的工具列上取得。

    ![Azure 入口網站工具列](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. 請確定環境已設定為**PowerShell**。 根據預設，它會設定為 Bash。

    ![PowerShell 工具列設定](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. 上傳 Cloud Shell 實例中步驟1的檔案。

    ![上傳工具列按鈕](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. 移至上傳檔案的目錄。 根據預設，檔案會上傳至使用者名稱底下的主目錄。

9. 執行下列指令碼。 腳本會要求提供可從 Azure Active Directory > 總覽頁面取得的租使用者識別碼。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. 依照畫面上的指示來捕獲**API 端點**、租使用者**識別碼**、**用戶端識別碼**、**用戶端密碼**和**EventHub 連接字串**的值。

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>使用產生的認證來整合裝置資料

前往裝置合作夥伴入口網站，使用您在上一節中產生的一組認證來連結 FarmBeats：

 - API 端點
 - EventHub 連接字串
 - 用戶端識別碼
 - 用戶端密碼
 - 租用戶識別碼

 裝置提供者會確認整合成功。 確認之後，您可以在 Azure FarmBeats 上查看所有裝置和感應器。

## <a name="view-devices-and-sensors"></a>查看裝置和感應器

請使用下一節來查看伺服器陣列的裝置和感應器。

### <a name="view-devices"></a>檢視裝置

目前，FarmBeats 支援下列裝置：

- **節點**：一或多個感應器所連接的裝置。
- **閘道**：一或多個節點所連接的裝置。

請遵循下列步驟。

1. 在 [首頁] 頁面上，從功能表中選取 [**裝置**]。
  [**裝置**] 頁面會顯示裝置類型、型號、狀態、其放置所在的伺服器陣列，以及中繼資料的上次更新日期。 根據預設，[伺服器陣列] 資料行會設定為*Null*。 您可以選擇將裝置指派給伺服器陣列。 如需詳細資訊，請參閱[指派裝置](#assign-devices)。
2. 選取裝置以查看連線到裝置的裝置屬性、遙測和子裝置。

    ![[裝置] 頁面](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>觀看感應器

請遵循下列步驟。

1. 在 [首頁] 頁面上，從功能表中選取 [**感應器**]。
  [**感應器**] 頁面會顯示感應器類型、其所連線的伺服器陣列、[父裝置]、[埠名稱]、[埠類型] 和 [上次更新] 狀態的詳細資料。
2. 選取感應器以查看感應器內容、作用中警示和感應器的遙測。

    ![感應器頁面](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>指派裝置  

感應器資料流程入之後，您可以將它指派給您部署感應器的伺服器陣列。

1. 在 [首頁] 頁面上，從功能表中選取 [**伺服器**陣列]。 [**伺服器**陣列清單] 頁面隨即出現。
2. 選取您要指派裝置的伺服器陣列，然後選取 [**新增裝置**]。
3. [**新增裝置**] 視窗隨即出現。 選取您要指派給伺服器陣列的裝置。

    ![[新增裝置] 視窗](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選取 [**新增裝置**]。 或者，移至 [**裝置**] 功能表，選取您要指派給伺服器陣列的裝置，然後選取 [**關聯裝置**]。
5. 在 [**關聯裝置**] 視窗中，從下拉式清單中選取伺服器陣列，然後選取 [套用**到全部**]，讓伺服器陣列與所有選取的裝置產生關聯。

    ![關聯裝置視窗](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 若要將每個裝置關聯到不同的伺服器陣列，請選取 [**指派給伺服器**陣列] 資料行中的下拉箭號，然後選取每個裝置資料列的伺服器陣列。
7. 選取 [**指派**] 以完成裝置指派。

### <a name="visualize-sensor-data"></a>將感應器資料視覺化

請遵循下列步驟。

1. 在 [首頁] 頁面上，從功能表中選取 [**伺服器**陣列] 以查看 [**伺服器**陣列] 頁面。
2. 選取您要查看其感應器資料的**伺服器**陣列。
3. 在 [**伺服器**陣列] 儀表板上，您可以查看遙測資料。 您可以觀看即時遙測，或使用**自訂範圍**來查看特定的日期範圍。

    ![伺服器陣列儀表板](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>刪除感應器

請遵循下列步驟。

1. 在 [首頁] 頁面上，從功能表選取 [**感應器**] 以查看 [**感應器**] 頁面。
2. 選取您要刪除的裝置，然後在確認視窗中選取 [**刪除**]。

    ![刪除按鈕](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

確認訊息會顯示已成功刪除感應器。

## <a name="delete-devices"></a>刪除裝置

請遵循下列步驟。

1. 在 [首頁] 頁面上，從功能表中選取 [**裝置**] 以查看 [**裝置**] 頁面。
2. 選取您要刪除的裝置，然後在確認視窗中選取 [**刪除**]。

    ![刪除按鈕](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>後續步驟

您現在已將感應器資料流程入您的 Azure FarmBeats 實例。 現在，請瞭解如何為您的伺服器陣列[產生對應](generate-maps-in-azure-farmbeats.md#generate-maps)。
