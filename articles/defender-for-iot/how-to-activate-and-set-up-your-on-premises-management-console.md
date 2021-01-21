---
title: 啟用和設定內部部署管理主控台
description: 管理主控台啟用和設定可確保感應器已向 Azure 註冊，並將資訊傳送至內部部署管理主控台，而且內部部署管理主控台會在連線的感應器上執行管理工作。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 94a1db30419e5d7e52f369392d94b817d0dc273a
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623749"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>啟用和設定內部部署管理主控台 

啟用和設定內部部署管理主控台可確保：

- 您透過連線感應器監視的網路裝置會向 Azure 帳戶註冊。

- 感應器會將資訊傳送至內部部署管理主控台。

- 內部部署管理主控台會在連線的感應器上執行管理工作。

- 您已安裝 SSL 憑證。

## <a name="sign-in-for-the-first-time"></a>第一次登入

若要登入管理主控台：

- 開啟網頁瀏覽器，並輸入系統安裝期間在內部部署管理主控台收到的 IP 位址和密碼。 如果您忘記密碼，請選取 [ **復原密碼** ]，然後查看 [ [密碼](how-to-manage-the-on-premises-management-console.md#password-recovery)復原]。

## <a name="upload-an-activation-file"></a>上傳啟用檔案

第一次登入之後，請從 Azure Defender for IoT 入口網站的 [ **定價** ] 頁面下載啟用檔案，以啟用內部部署管理主控台。 此檔案包含在上架程式期間定義的匯總認可裝置。 **認可的裝置** 會指出 Defender for IoT 將針對每個訂用帳戶監視的裝置數目。

上傳啟用檔案：

1. 移至 [適用于 IoT 的 Defender **定價** ] 頁面。
1. 選取 [ **下載管理主控台** ] 索引標籤的啟用檔。啟用檔案已下載。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="下載啟用檔。":::

1. 從管理主控台選取 [ **系統設定** ]。
1. 選取 [ **啟用**]。
1. 選取 **[選擇** 檔案]，然後選取您儲存的檔案。

初始啟用之後，受監視裝置的數目可能會超過上線期間所定義的認可裝置數目。 例如，如果您將更多感應器連線到管理主控台，就可能發生這種情況。 如果受監視的裝置數目與認可的裝置數目之間有不一致的情況，則會在管理主控台中顯示警告。 如果發生這種情況，您應該上傳新的啟用檔。

## <a name="set-up-a-certificate"></a>設定憑證

管理主控台安裝之後，會產生本機自我簽署憑證，並使用此憑證來存取主控台。 當系統管理員第一次登入管理主控台時，系統會提示該使用者上架 SSL/TLS 憑證。 

有兩種安全性層級可用：

- 上傳 CA 簽署的憑證，以符合您組織所要求的特定憑證和加密需求。
- 允許在管理主控台和連線感應器之間進行驗證。 驗證會針對憑證撤銷清單和憑證到期日進行評估。 *如果驗證失敗，管理主控台和感應器之間的通訊會停止，並在主控台中顯示驗證錯誤。* 安裝之後，預設會啟用此選項。  

主控台支援下列類型的憑證：

- 私用和企業金鑰基礎結構 (私用 PKI) 

- 公開金鑰基礎結構 (公開 PKI) 

- 在本機產生的設備 (本機自我簽署)  

  > [!IMPORTANT]
  > 我們建議您不要使用自我簽署的憑證。 憑證並非安全的，且只能用於測試環境。 憑證的擁有者無法驗證，而且無法維護系統的安全性。 請勿將此選項用於生產網路。

若要上傳憑證：

1. 登入後出現提示時，請定義憑證名稱。
1. 上傳 CRT 和金鑰檔。
1. 輸入複雜密碼，然後視需要上傳 PEM 檔案。

上傳 CA 簽署的憑證之後，您可能需要重新整理畫面。

若要停用管理主控台與連線感應器之間的驗證：

1. 選取 [下一步]  。
1. 關閉 [ **啟用全系統驗證** ] 切換。

如需上傳新憑證、支援的憑證檔案和相關專案的詳細資訊，請參閱 [管理內部部署管理主控台](how-to-manage-the-on-premises-management-console.md)。

## <a name="connect-sensors-to-the-on-premises-management-console"></a>將感應器連線至內部部署管理主控台

您必須確定感應器會將資訊傳送至內部部署管理主控台，而且內部部署管理主控台可以執行備份、管理警示，以及執行感應器上的其他活動。 若要這樣做，請使用下列程式來確認您是在感應器和內部部署管理主控台之間進行初始連線。

有兩個選項可用來將適用于 IoT 的 Azure Defender 感應器連線至內部部署管理主控台：

- 從感應器主控台連接

- 使用通道連接

連接之後，您必須使用這些感應器來設定網站。

### <a name="connect-sensors-from-the-sensor-console"></a>從感應器主控台連接感應器

從感應器主控台將特定感應器連線至內部部署管理主控台：

1. 在感應器主控台的左窗格中，選取 [ **系統設定**]。

2. 選取 [ **與管理的連接**]。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="內部部署管理主控台的 [狀態] 視窗的螢幕擷取畫面，其中顯示 [未連線]。":::

3. 在 [ **位址** ] 文字方塊中，輸入您要連線的內部部署管理主控台的 IP 位址。

4. 選取 [連線]。 狀態會變更：

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="內部部署管理主控台的 [狀態] 視窗的螢幕擷取畫面，其中顯示 [已連線]。":::

### <a name="connect-sensors-by-using-tunneling"></a>使用通道連接感應器

在組織感應器和內部部署管理主控台之間啟用安全通道連線。 這種設定會規避與組織防火牆的互動，因此可減少受攻擊面。

使用通道可讓您從 IP 位址和單一 (埠（9000) 到任何感應器）連接到內部部署管理主控台。

若要在內部部署管理主控台設定通道：

- 登入內部部署管理主控台，然後執行下列命令：

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

若要在感應器上設定通道：

1. 在感應器 (網路上開啟 TCP 通訊埠9000。屬性) 手動。 如果埠未開啟，感應器將會拒絕來自內部部署管理主控台的連線。

2. 登入每個感應器，然後執行下列命令：

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>設定網站

預設的企業地圖會根據數個層級的地理位置來提供裝置的整體觀點。

在組織結構和使用者權限很複雜的情況下，可能需要您的裝置視圖。 在這些情況下，除了標準網站或區域結構之外，網站設定可能是由通用群組織結構所決定。

若要支援此環境，您需要建立以您組織的業務單位、區域、網站和區域為基礎的全球商務拓朴。 您也需要使用存取群組來定義這些實體的使用者存取權限。

存取群組可讓您更有效地控制使用者在 Defender for IoT 平臺中管理和分析裝置的位置。

### <a name="how-it-works"></a>運作方式

您可以針對每個網站定義業務單位和區域。 然後，您可以新增區域，也就是您網路中的邏輯實體。 

您應該為每個區域指派至少一個感應器。 這五層級的模型提供了提供保護系統以反映組織結構所需的彈性和細微性。

您可以直接從任何地圖視圖中編輯您的網站。 當您從地圖視圖開啟網站時，開啟的警示數目會出現在每個區域的旁邊。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="包含柏林資料重迭之內部部署管理主控台地圖的螢幕擷取畫面。":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="顯示感應器和區域關聯性的圖表。":::

若要設定網站：

1. 加入新的業務單位，以反映貴組織的邏輯結構。

2. 新增區域以反映貴組織的區域。

3. 新增網站。

4. 將區域新增至網站。

5. 連接感應器。

6. 將感應器指派給網站區域。

若要加入業務單位：

1. 從企業版視圖中，選取 [**所有網站**  >  **管理業務單位**]。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="顯示 [管理商務單位] 視圖的螢幕擷取畫面。":::

2. 輸入新的業務單位名稱，然後 **選取 [新增]**。

若要新增區域：

1. 從企業版視圖中，選取 [**所有區域**  >  **管理區域**]。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="顯示 [管理區域] 視圖的螢幕擷取畫面。":::

2. 輸入新的區功能變數名稱稱，然後 **選取 [新增]**。

若要加入新的網站：

1. 從企業版視圖中，選取 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: 頂端列。 您的游標會顯示為加號 (**+**) 。

2. 將放置在 **+** 新網站的位置，並加以選取。 [ **建立新網站** ] 對話方塊隨即開啟。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="[建立新網站] 視圖的螢幕擷取畫面。":::

3. 定義新網站的名稱和實體位址，然後選取 [ **儲存**]。 新網站會出現在網站地圖上。

若要刪除網站：

1. 在 [ **網站管理** ] 視窗中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 從包含網站名稱的列中選取，然後選取 [ **刪除網站**]。 確認方塊隨即出現，確認您想要刪除網站。

2. 在確認方塊中，選取 **[是]**。 確認方塊會關閉，而且 **網站管理** 視窗會出現，而不會顯示您已刪除的網站。

## <a name="create-enterprise-zones"></a>建立企業區域

區域是邏輯實體，可讓您根據各種特性，將網站內的裝置分割成群組。 例如，您可以建立產品線、變電站、網站區域或裝置類型的群組。 您可以根據適合您組織的任何特性來定義區域。

您可以將區域設定為網站設定流程的一部分。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="[網站管理區域] 視圖的螢幕擷取畫面。":::

下表說明 [ **網站管理** ] 視窗中的參數。

| 參數 | 描述 |
|--|--|
| 名稱 | 感應器的名稱。 您只能從感應器變更此名稱。 如需詳細資訊，請參閱 Defender for IoT 使用者指南。 |
| IP | 感應器 IP 位址。 |
| 版本 | 感應器版本。 |
| 連線能力 | 感應器連接狀態。 狀態可以是 [ **已連線** ] 或 [已 **中斷** 連線]。 |
| 上次升級 | 上次升級的日期。 |
| 升級進度 | 進度列會顯示升級程式的狀態，如下所示：<br />-上傳套件<br />-正在準備安裝<br />-正在停止進程<br />-備份資料<br />-拍攝快照集<br />-正在更新設定<br />-正在更新相依性<br />-更新程式庫<br />-修補資料庫<br />-正在啟動進程<br />-正在驗證系統的健全<br />-驗證成功<br />-Success<br />-失敗<br />-已開始升級<br />-正在啟動安裝ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >如需升級的詳細資訊，請參閱 [Microsoft 支援服務](https://support.microsoft.com/) 尋求協助。 |
| 裝置 | 感應器所監視的 OT 裝置數目。 |
| 警示 | 感應器上的警示數目。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | 可將感應器指派至區域。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| 可從網站刪除已中斷連線的感應器。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | 指出目前已連線到區域的感應器數目。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | 指出目前已連線到區域的多個 OT 資產。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | 指出指派給區域的感應器所傳送的警示數目。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | 從區域即取消感應器。 |

若要將區域新增至網站：

1. 在 [ **網站管理** ] 視窗中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 從包含網站名稱的列中選取，然後選取 [ **新增區域**]。 [ **建立新區域** ] 對話方塊隨即出現。

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="[建立新區域] 視圖的螢幕擷取畫面。":::

2. 輸入區功能變數名稱稱。

3. 輸入新區域的描述，以明確指出您用來將網站分成區域的特性。

4. 選取 [儲存]  。 新區域會出現在 [ **網站管理** ] 視窗中，位於此區域所屬的網站下方。

若要編輯區域：

1. 在 [ **網站管理** ] 視窗中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 從包含區功能變數名稱稱的列中選取，然後選取 [ **編輯區域**]。 [ **編輯區域** ] 對話方塊隨即出現。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="顯示 [編輯區域] 對話方塊的螢幕擷取畫面。":::

2. 編輯區域參數，然後選取 [ **儲存**]。

若要刪除區域：

1. 在 [ **網站管理** ] 視窗中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 從包含區功能變數名稱稱的列中選取，然後選取 [ **刪除區域**]。

2. 在確認方塊中，選取 **[是]**。

若要根據連接狀態進行篩選：

- 從左上角，選取 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: [連線] 旁的[下一步]，然後選取下列其中一個選項：

  - **全部**：提供報告至此內部部署管理主控台的所有感應器。

  - **已連線**：只會顯示已連線的感應器。

  - 已 **中斷** 連線：只顯示已中斷連線的感應器。

若要根據升級狀態進行篩選：

- 從左上角選取 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: [ **升級狀態** ] 旁的 [下一步]，然後選取下列其中一個選項：

  - **全部**：提供報告至此內部部署管理主控台的所有感應器。

  - **有效**：顯示具有有效升級狀態的感應器。

  - **進行中**：顯示正在進行升級的感應器。

  - **Failed**：顯示升級程式失敗的感應器。

## <a name="assign-sensors-to-zones"></a>將感應器指派至區域

針對每個區域，您需要指派執行本機流量分析和警示的感應器。 您只能指派連接至內部部署管理主控台的感應器。

若要指派感應器：

1. 選取 [ **網站管理**]。 未指派的感應器會出現在對話方塊的左上角。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="未指派感應器視圖的螢幕擷取畫面。":::

2. 確認 **連接** 狀態為 [已連線]。 如果沒有，請參閱 [將感應器連接至內部部署管理主控台](#connect-sensors-to-the-on-premises-management-console) ，以取得連接的詳細資料。 

3. 選取 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: 您要指派的感應器。

4. 在 [ **指派感應器** ] 對話方塊中，選取要指派的業務單位、區域、網站和區域。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="指派感應器視圖的螢幕擷取畫面。":::

5. 選取 [ **指派**]。

若要取消指派和刪除感應器：

1. 中斷感應器與內部部署管理主控台的連線。 如需詳細資訊，請參閱 [將感應器連線至內部部署管理主控台](#connect-sensors-to-the-on-premises-management-console) 。

2. 在 [ **網站管理** ] 視窗中，選取感應器並選取 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: 。 感應器會在幾分鐘後出現在未指派感應器的清單中。

3. 若要從網站刪除未指派的感應器，請從未指派的感應器清單中選取感應器，然後選取 [] :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: 。

## <a name="see-also"></a>另請參閱

[對感應器和內部部署管理主控台進行疑難排解](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
