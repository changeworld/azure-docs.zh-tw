---
title: 使用 Azure 備份伺服器備份 Exchange server
description: 了解如何使用 Azure 備份伺服器將 Exchange Server 備份至 Azure 備份
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 2e3e89d93ab35724a09138d48863badad6a41b71
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514113"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>使用 Azure 備份伺服器將 Exchange Server 備份至 Azure

本文說明如何設定 Microsoft Azure 備份伺服器 (MABS)，以將 Microsoft Exchange Server 備份至 Azure。  

## <a name="prerequisites"></a>先決條件

繼續之前，請確定已[安裝並備妥](backup-azure-microsoft-azure-backup.md) Azure 備份伺服器。

## <a name="mabs-protection-agent"></a>MABS 保護代理程式

若要在 Exchange Server 上安裝 MABS 保護代理程式，請遵循下列步驟：

1. 請確定已正確設定防火牆。 請參閱 [設定代理程式的防火牆例外狀況](/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019)。
2. 按一下 MABS 系統管理員主控台中的 [管理] > [代理程式] > [安裝]****，在 Exchange Server 上安裝代理程式。 如需詳細步驟，請參閱[安裝 MABS 保護代理程式](/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) 。

## <a name="create-a-protection-group-for-the-exchange-server"></a>建立 Exchange Server 的保護群組

1. 在 MABS 系統管理員主控台中，按一下 [保護]****，然後按一下工具功能區上的 [新增]****，以開啟 [建立新保護群組]**** 精靈。
2. 在嚮導的**歡迎**畫面上，按 **[下一步]**。
3. 在 [**選取保護群組類型**] 畫面上，選取 [**伺服器**] 並按 **[下一步]**。
4. 選取您想要保護的 Exchange Server 資料庫，然後按 [下一步] ****。

   > [!NOTE]
   > 如果您要保護 Exchange 2013，請檢查 [Exchange 2013 先決條件](/system-center/dpm/back-up-exchange)。
   >
   >

    下例中選取了Exchange 2010 資料庫。

    ![選擇群組成員](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. 選取資料保護方式。

    替保護群組命名，然後選取下列兩個選項：

   * 我想要使用磁碟進行短期保護。
   * 我想要線上保護。
6. 按 [下一步] 。
7. 如果您想要檢查 Exchange Server 資料庫的完整性，請選取 [執行 Eseutil 以檢查資料完整性] **** 選項。

    選取此選項之後，將會在 MABS 上執行備份一致性檢查，以避免在 Exchange server 上執行**eseutil**命令所產生的 i/o 流量。

   > [!NOTE]
   > 若要使用此選項，您必須將 Ese.dll 和 Eseutil.exe 檔案複製到 MABS 伺服器上的 C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin 目錄。 否則會觸發下列錯誤：  
   > ![eseutil 錯誤](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. 按 [下一步] 。
9. 選取用於 [複製備份]**** 的資料庫，然後按 [下一步]****。

   > [!NOTE]
   > 如果您未針對資料庫的至少一個 DAG 複本選取「完整備份」，則不會截斷記錄。
   >
   >
10. 設定 [短期備份]**** 的目標，然後按 [下一步]****。
11. 檢閱可用的磁碟空間，然後按 [下一步] ****。
12. 選取 MABS 伺服器將建立初始複寫的時間，然後按 **[下一步]**。
13. 選取一致性檢查選項，然後按 [下一步] ****。
14. 選擇您要備份至 Azure 資料庫，然後按 [下一步] ****。 例如：

    ![指定線上保護資料](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. 定義 [Azure 備份]**** 的排程，然後按 [下一步]****。 例如：

    ![指定線上備份排程](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > 請注意，線上復原點是以快速完整復原點為基礎。 因此，您必須在指定給快速完整復原點的時間之後，排程線上復原點。
    >
    >
16. 設定 [Azure 備份]**** 的保留原則，然後按 [下一步]****。
17. 選擇線上複寫選項並按 [下一步] ****。

    如果您有大型資料庫，則透過網路建立初始備份所需的時間很長。 若要避免這個問題，您可以建立離線備份。  

    ![指定線上保留期原則](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. 確認設定，然後按一下 [建立群組] ****。
19. 按一下 [關閉] 。

## <a name="recover-the-exchange-database"></a>復原 Exchange 資料庫

1. 若要復原 Exchange 資料庫，請按一下 MABS 系統管理員主控台中的 [復原]**** 。
2. 找出您要復原的 Exchange 資料庫。
3. 從「復原時間」 ** 下拉式清單選取線上復原點。
4. 按一下 [復原]**** 啟動 [復原精靈]****。

線上復原點有五種復原類型：

* **復原到原始 Exchange Server 位置：** 資料將會還原到原始 Exchange Server。
* **復原到 Exchange Server 上的其他資料庫：** 資料將會還原到其他 Exchange Server 上的其他資料庫。
* **復原到復原資料庫：** 資料將會復原到 Exchange 復原資料庫 (RDB)。
* **複製到網路資料夾：** 資料將會還原到網路資料夾。
* **複製到磁帶：** 如果您已在 MABS 上連接並設定磁帶媒體櫃或獨立磁帶機，則會將復原點複製到可用的磁帶。

    ![選擇線上複寫](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>後續步驟

* [Azure 備份常見問題集](backup-azure-backup-faq.md)
