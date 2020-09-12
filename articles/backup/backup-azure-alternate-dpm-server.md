---
title: 從 Azure 備份伺服器復原資料
description: 從登錄至復原服務保存庫的任何 Azure 備份伺服器，復原該保存庫中保護的資料。
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 0cce45fbaaedad4cfcee0cda768aae2fa294312b
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378061"
---
# <a name="recover-data-from-azure-backup-server"></a>從 Azure 備份伺服器復原資料

您可以使用 Azure 備份伺服器，將您已備份到復原服務保存庫的資料復原。 要這麼做的程序就是整合到 Azure 備份伺服器管理主控台，且類似於其他 Azure 備份元件的復原工作流程。

> [!NOTE]
> 本文適用於 [System Center Data Protection Manager 2012 R2 with UR7 或更新版本](https://support.microsoft.com/kb/3065246) \(英文\)，結合[最新版的 Azure 備份代理程式](https://aka.ms/azurebackup_agent)。
>
>

若要從 Azure 備份伺服器復原資料：

1. 從 Azure 備份伺服器管理 **主控台的 [復原] 索引** 標籤中，選取畫面左上方的 [ **新增外部 DPM** ] () 。

    ![新增外部 DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. 從與要復原資料的 **Azure 備份伺服器**關聯的保存庫下載新的**保存庫認證**、從已向復原服務保存庫登錄的 Azure 備份伺服器清單中選擇 Azure 備份伺服器，並提供與要復原資料的伺服器關聯的**加密複雜密碼**。

    ![外部 DPM 認證](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > 只有與相同登錄保存庫相關聯的 Azure 備份伺服器，才可以復原彼此的資料。
   >
   >

    順利新增外部 Azure 備份伺服器之後，您就可以從 [復原]**** 索引標籤瀏覽外部伺服器和本機 Azure 備份伺服器的資料。
3. 瀏覽由外部 Azure 備份伺服器保護的實際執行伺服器可用清單，並選取適當的資料來源。

    ![瀏覽外部 DPM 伺服器](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. 從 [復原點]**** 下拉式清單中選取「月份和年份」****，選取代表復原點何時建立的必要 [復原日期]****，並選取 [復原時間]****。

    檔案和資料夾清單會出現在底部窗格中，方便您瀏覽並復原到任何位置。

    ![外部 DPM 伺服器復原點](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. 以滑鼠右鍵按一下適當的專案，然後選取 [ **復原**]。

    ![外部 DPM 復原](./media/backup-azure-alternate-dpm-server/recover.png)
6. 檢閱「復原選取項目」 ****。 請確認要復原之備份複本的資料和時間，以及建立備份複本的來源。 如果選取專案不正確，請選取 [ **取消** ] 以流覽回 [復原] 索引標籤，以選取適當的復原點。 如果選取的是正確的，請選取 **[下一步]**。

    ![外部 DPM 復原摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. 選取 [復原到替代位置] ****。 **** 到正確的復原位置。

    ![外部 DPM 復原替代位置](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. 選擇與 [建立複本]****、[略過]**** 或 [覆寫]**** 相關的選項。

   * **建立複本** -如果發生名稱衝突，則建立檔案的複本。
   * **略過** -如果有名稱衝突，它就不會復原檔案，該檔案會離開原始檔案。
   * **覆寫** -如果有名稱衝突，則會覆寫現有的檔案複本。

     請選擇適當的選項來「還原安全性」 ****。 您可以在建立復原點時，針對將資料復原至其中的目的地電腦，套用其安全性設定，或是套用適用於產品的安全性設定。

     確認是否要在復原成功完成後，傳送「通知」****。

     ![外部 DPM 復原通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. [摘要] **** 畫面會列出到目前為止已選擇的選項。 一旦您選取 [ **復原**]，資料就會復原到適當的內部部署位置。

    ![外部 DPM 復原選項摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > 您可以在 Azure 備份伺服器的 [監視]**** 索引標籤中監視復原作業。
   >
   >

    ![監視復原](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. 您可以在 DPM**伺服器的 [復原] 索引**標籤上選取 [**清除外部 dpm** ]，以移除外部 dpm 伺服器的觀點。

    ![清除外部 DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>疑難排解錯誤訊息

| 不會。 | 錯誤訊息 | 疑難排解步驟 |
|:---:|:--- |:--- |
| 1. |保存庫認證所指定的保存庫中未登錄此伺服器。 |**原因：** 如果選取的保存庫認證檔案不屬於與所嘗試復原之 Azure 備份伺服器關聯的復原服務保存庫，就會出現此錯誤。 <br> **解決方法：** 從已登錄 Azure 備份伺服器的復原服務保存庫下載保存庫認證檔。 |
| 2. |可復原資料無法使用，或選取的伺服器不是 DPM 伺服器。 |**原因：** 沒有其他 Azure 備份伺服器向復原服務保存庫登錄，或伺服器尚未上傳中繼資料，或選取的伺服器不是使用 Windows Server 或 Windows 用戶端) 的 Azure 備份伺服器 (。 <br> **解決方法：** 如果有其他已向復原服務保存庫登錄的 Azure 備份伺服器，請確定已安裝最新的 Azure 備份代理程式。 <br>如果有其他 Azure 備份伺服器已向復原服務保存庫登錄，請在安裝後等候一天，再開始復原程序。 夜間作業會針對所有受保護的備份，將中繼資料上傳至雲端。 資料將可供復原。 |
| 3. |此保存庫未登錄其他 DPM 伺服器。 |**原因︰** 沒有任何其他 Azure 備份伺服器已向嘗試復原的保存庫登錄。<br>**解決方法：** 如果有其他已向復原服務保存庫登錄的 Azure 備份伺服器，請確定已安裝最新的 Azure 備份代理程式。<br>如果有其他 Azure 備份伺服器已向復原服務保存庫登錄，請在安裝後等候一天，再開始復原程序。 夜間作業會針對所有受保護的備份，將中繼資料上傳至雲端。 資料將可供復原。 |
| 4. |提供的加密複雜密碼與下列伺服器關聯的複雜密碼不相符： **\<server name>** |**原因：** 從正在復原之 Azure 備份伺服器資料中加密資料的程式中所使用的加密複雜密碼，與所提供的加密複雜密碼不符。 代理程式無法解密資料，因此復原會失敗。<br>**解決方式：** 提供與正在復原其資料的 Azure 備份伺服器相關聯的相同加密複雜密碼。 |

## <a name="next-steps"></a>接下來的步驟

閱讀其他常見問題集：

* 關於 Azure VM 備份的[常見問題](backup-azure-vm-backup-faq.md)
* 「Azure 備份」代理程式的相關[常見問題](backup-azure-file-folder-backup-faq.md)
