---
title: 從內部部署管理主控台管理感應器
description: 瞭解如何從管理主控台管理感應器，包括更新感應器版本、將系統設定推送至感應器，以及啟用和停用感應器上的引擎。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 36db1b23d8fb17cec4fe981c938f8c7003543b4d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838722"
---
# <a name="manage-sensors-from-the-management-console"></a>從管理主控台管理感應器

本文說明如何從管理主控台管理感應器，包括：

- 將系統設定推送至感應器

- 啟用和停用感應器上的引擎

- 更新感應器版本

## <a name="push-configurations"></a>推送設定

您可以定義各種系統設定，並自動將它們套用到連線到管理主控台的感應器。 這可節省時間，並協助確保企業感應器之間的簡化設定。

您可以從管理主控台定義下列感應器系統設定：

- 郵件伺服器

- SNMP MIB 監視

- Active Directory

- DNS 設定

- 子網路

- 埠別名

若要套用系統設定：

1. 在主控台的左窗格中，選取 [ **系統設定**]。

2. 在 [ **設定感應器** ] 窗格中，選取其中一個選項。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="感應器的系統設定選項。":::

   下列範例說明如何定義企業感應器的郵件伺服器參數。

3. 選取 [ **郵件伺服器**]。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="從 [系統設定] 畫面中選取您的郵件伺服器。":::

4. 選取左側的感應器。

5. 設定郵件伺服器參數，然後選取 [ **重複**]。 感應器樹狀結構中的每個專案都會顯示其旁邊的核取方塊。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="確定已為您的感應器選取核取方塊。":::

6. 在感應器樹狀結構中，選取您要套用設定的專案。

7. 選取 [儲存]。

## <a name="update-versions"></a>更新版本

您可以從內部部署管理主控台同時更新數個感應器。

若要更新數個感應器：

1. 移至 [Azure 入口網站](https://portal.azure.com/)。

2. 移至適用于 IoT 的 Azure Defender。

3. 移至 [ **更新** ] 頁面。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="更新儀表板視圖的螢幕擷取畫面。":::

4. 從 [**感應器**] 區段中選取 [**下載**]，然後儲存檔案。

5. 登入管理主控台，然後選取 [ **系統設定**]。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="選取 [系統設定] 的 [管理] 功能表螢幕擷取畫面。":::

6. 在 [ **感應器引擎** 設定] 區段中標示您想要更新的感應器，然後選取 [ **自動更新**]。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="兩個顯示學習模式和自動更新的感應器。":::

7. 選取 [Save Changes] \(儲存變更\)。

8. 在 [ **感應器版本升級** ] 窗格中，選取 :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="顯示檔案的感應器版本升級畫面。":::

9. **[上傳** 檔案] 對話方塊隨即開啟。 上傳您從 [ **更新** ] 頁面下載的檔案。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="選取 [流覽] 按鈕來上傳您的檔案。":::

10. 在更新過程中，每個感應器的更新狀態都會出現在 [ **網站管理** ] 視窗中。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="觀察更新的進度。":::

## <a name="update-threat-intelligence-packages"></a>更新威脅情報套件 

每個新的 Defender for IoT 版本都會提供威脅情報的資料套件，或在版本之間有需要時提供。 封裝包含簽章 (包括惡意程式碼簽章) 、Cve 和其他安全性內容。 

您可以從 Defender for IoT 入口網站的 [ **更新** ] 頁面手動上傳此檔案，並自動將其更新為感應器。 

若要更新威脅情報資料： 

1. 移至 [適用于 IoT 的 Defender **更新** ] 頁面。 

2. 下載並儲存檔案。

3. 登入管理主控台。 

4. 在側邊功能表上，選取 [ **系統設定**]。 

5. 在 [ **感應器引擎** 設定] 區段中，選取應接收更新的感應器。  

6. 在 [ **選取威脅情報資料** ] 區段中，選取加號 (**+**) 。 

7. 上傳您從 [Defender for IoT **Updates** ] 頁面下載的套件。

## <a name="understand-sensor-disconnection-events"></a>瞭解感應器中斷連接事件

如果感應器中斷與其指派的內部部署管理主控台的連線，[ **網站管理員** ] 視窗就會顯示中斷連線資訊。 以下是可用的感應器中斷連接資訊：

- 「內部部署管理主控台無法處理從感應器接收的資料。」

- 「偵測到時間漂移。 內部部署管理主控台已與感應器中斷連線」。

- 「感應器未與內部部署管理主控台通訊。 檢查網路連接或憑證驗證。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="區域1視圖的螢幕擷取畫面。":::

您可以使用已中斷連線的感應器相關資訊，將警示傳送給協力廠商。 如需詳細資訊，請參閱 [轉寄感應器失敗警示](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)。

## <a name="enable-or-disable-sensors"></a>啟用或停用感應器

感應器受適用于 IoT 引擎的五個 Defender 所保護。 您可以啟用或停用已連線感應器的引擎。

| 引擎 | 描述 | 範例案例 |
|--|--|--|
| 通訊協定違規引擎 | 當封包結構或域值不符合通訊協定規格時，就會發生通訊協定違規。 | 「不合法的 MODBUS 作業 (函式程式碼零) 」警示。 此警示表示主要裝置已傳送函式程式碼為0的要求至次要裝置。 根據通訊協定規格，不允許這種情況，而且次要裝置可能不會正確地處理輸入。 |
| 原則違規引擎 | 發生原則違規時，與學習或設定的原則中所定義的基準行為差異。 | 「未經授權的 HTTP 使用者代理程式」警示。 此警示表示原則未學習或核准的應用程式，會用來做為裝置上的 HTTP 用戶端。 這可能是新的網頁瀏覽器或該裝置上的應用程式。 |
| 惡意程式碼引擎 | 惡意程式碼引擎會偵測惡意的網路活動。 | 「可疑的惡意活動 (Stuxnet) 」警示。 此警示表示感應器找到已知與 Stuxnet 惡意程式碼相關的可疑網路活動，這是以產業控制和 SCADA 網路為目標的先進持續性威脅。 |
| 異常引擎 | 惡意程式碼引擎偵測到網路行為異常。 | 「通道中的定期行為」。 這是一項元件，可檢查網路連線，並尋找資料傳輸的定期或迴圈行為，這在產業網路中很常見。 |
| 操作引擎 | 此引擎會偵測到操作事件或有故障的實體。 | 「懷疑資產已中斷連線 (沒有回應) 」警示。 當裝置未回應預先定義期間內的任何要求時，就會觸發此警示。 這可能表示裝置關機、中斷連線或故障。
|

若要啟用或停用已連線感應器的引擎：

1. 在主控台的左窗格中，選取 [ **系統設定**]。

2. 在 [ **感應器引擎** 設定] 區段中，選取 [ **啟用** ] 或 [ **停** 用引擎]。
         
3. 選取 [ **儲存變更**]。

   如果您的其中一個企業感應器有不相符的啟用引擎，則會出現紅色驚嘆號。 引擎可能已直接從感應器停用。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="啟用的引擎不符。"::: 

## <a name="define-sensor-backup-schedules"></a>定義感應器備份排程

您可以排程感應器備份，並從內部部署管理主控台執行隨選感應器備份。 這有助於防止硬碟故障和資料遺失。

- 備份的內容：設定和資料。

- 未備份的內容： PCAP 檔案和記錄檔。 您可以手動備份和還原 PCAPs 和記錄。

依預設，感應器會每日自動備份到上午3:00。 感應器的 [備份排程] 功能可讓您收集這些備份，並將它們儲存在內部部署管理主控台或外部備份伺服器上。 從感應器將檔案複製到內部部署管理主控台，會透過加密的通道進行。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="感應器備份畫面的視圖。":::

當預設的感應器備份位置變更時，內部部署管理主控台會自動從感應器或外部位置上的新位置抓取檔案，但前提是主控台具有存取該位置的許可權。 

當感應器未向內部部署管理主控台註冊時，[ **感應器備份排程** ] 對話方塊會指出不會管理任何感應器。  

無論檔案的儲存位置為何，還原程式都相同。

### <a name="backup-storage-for-sensors"></a>感應器的備份儲存體

您可以使用內部部署管理主控台，針對每個受管理的感應器維護最多9個備份，但前提是備份檔案不會超過所配置的備份空間上限。 

可用空間的計算方式是根據您正在使用的管理主控台模型： 

- **生產模型**：預設儲存體是 40 GB;限制為 100 GB。 

- **中模型**：預設儲存體為 20 GB;限制為 50 GB。 

- **膝上型電腦型號**：預設儲存體為 10 GB;限制為 25 GB。 

- **精簡型模型**：預設儲存體為 2 GB;限制為 4 GB。 

- **堅固模型**：預設儲存體為 10 GB;限制為 25 GB。 

預設配置會顯示在 [ **感應器備份排程** ] 對話方塊中。 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="編輯郵件伺服器設定畫面。":::

當您備份到外部伺服器時，不會有任何儲存體限制。 不過，您必須在 [**感應器備份排程**  >  **自訂路徑**] 欄位中定義上限配置限制。 以下是支援的數位和字元： `/, a-z, A-Z, 0-9, and _` 。 

以下是超過配置儲存體限制的相關資訊：

- 如果您超過配置的儲存空間，就不會備份感應器。 

- 如果您正在備份一個以上的感應器，管理主控台會嘗試取出受管理感應器的感應器檔案。  

- 如果某個感應器的抓取超過限制，管理主控台會嘗試從下一個感應器取出備份資訊。 

當您超過所定義的備份保留數目時，會刪除最舊的備份檔案以容納新的備份檔案。

感應器備份檔案會自動以下列格式命名： `<sensor name>-backup-version-<version>-<date>.tar` 。 例如： `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar` 。 

若要備份感應器：

1. 從 [**系統設定**] 視窗中選取 [**排程感應器備份**]。 您的內部部署管理主控台所管理的感應器會出現在 [ **感應器備份排程** ] 對話方塊中。  

2. 啟用 [ **收集備份** ] 切換。  

3. 選取行事曆間隔、日期和時區。 時間格式是以24小時制為基礎。 例如，輸入 6:00 PM 作為 **18:00**。 

4. 在 [ **備份儲存體配置** ] 欄位中，輸入您要為備份配置的儲存體。 如果您超過最大空間，就會收到通知。

5. 在 [ **保留最後一個** ] 欄位中，指出您要保留之每個感應器的備份數目。 當超過此限制時，就會刪除最舊的備份。  

6. 選擇備份位置：  

   - 若要備份至內部部署管理主控台，請停用 **自訂路徑** 切換。 預設位置為 `/var/cyberx/sensor-backups`。  

   - 若要備份到外部伺服器，請啟用 [ **自訂路徑** ] 切換，然後輸入位置。 以下是支援的數位和字元： `/, a-z, A-Z, 0-9, and, _` 。 

7. 選取 [儲存]。 

若要立即備份： 

- 選取 [ **立即備份**]。 內部部署管理主控台會建立並收集感應器備份檔案。 

### <a name="receiving-backup-notifications-for-sensors"></a>接收感應器的備份通知 

[ **感應器備份排程** ] 對話方塊和備份記錄檔會自動列出備份成功和失敗的相關資訊。  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="查看您的感應器以及它們所在的位置，以及所有相關資訊。":::

可能會因為下列原因而發生失敗：    

- 找不到任何備份檔案。 

- 找不到檔案，但無法抓取。  

- 網路連接失敗。 

- 配置給內部部署管理主控台的空間不足，無法完成備份。  

當發生失敗時，您可以傳送電子郵件通知、syslog 更新和系統通知。 若要這樣做，請在 **系統通知** 中建立轉送規則。 

### <a name="restoring-sensors"></a>還原感應器 

您可以從內部部署管理主控台和使用 CLI 來還原備份。  

若要從主控台還原： 

- 從 [**感應器系統** 設定] 視窗中選取 [**還原映射**]。

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="還原映射的備份。":::

  主控台接著會顯示還原失敗。  

若要使用 CLI 進行還原： 

- 登入系統管理帳戶，然後輸入 `$ sudo cyberx-xsense-system-restore` 。 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>將感應器備份儲存至外部 SMB 伺服器

若要設定 SMB 伺服器，讓您可以將感應器備份儲存到外部磁片磁碟機： 

1. 在外部 SMB 伺服器中建立共用資料夾。 

2. 取得存取 SMB 伺服器所需的資料夾路徑、使用者名稱和密碼。 

3. 在適用于 IoT 的 Defender 中，建立備份的目錄： 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. 編輯 fstab：  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. 編輯或建立要共用的認證。 以下是 SMB 伺服器的認證： 

   `sudo nano /etc/samba/user` 

6. 新增：  

   `username=<user name>` 

   `password=<password>` 

7. 掛接目錄： 

   `sudo mount -a` 

8. 設定備份目錄至適用于 IoT 的 Defender 上的共用資料夾感應器：  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. 將 `Backup.shared_location` 設定為 `<backup_folder_name_on_cyberx_server>`。

## <a name="see-also"></a>請參閱

[管理個別感應器](how-to-manage-individual-sensors.md)
