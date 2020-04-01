---
title: 使用 Azure 備份伺服器備份 Exchange 伺服器
description: 了解如何使用 Azure 備份伺服器將 Exchange Server 備份至 Azure 備份
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421342"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>使用 Azure 備份伺服器將 Exchange Server 備份至 Azure

本文說明如何設定 Microsoft Azure 備份伺服器 (MABS)，以將 Microsoft Exchange Server 備份至 Azure。  

## <a name="prerequisites"></a>Prerequisites

繼續之前，請確定已[安裝並備妥](backup-azure-microsoft-azure-backup.md) Azure 備份伺服器。

## <a name="mabs-protection-agent"></a>MABS 保護代理程式

若要在 Exchange Server 上安裝 MABS 保護代理程式，請遵循下列步驟：

1. 請確定已正確設定防火牆。 請參閱 [設定代理程式的防火牆例外狀況](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019)。
2. 按一下 MABS 系統管理員主控台中的 [管理] > [代理程式] > [安裝]****，在 Exchange Server 上安裝代理程式。 如需詳細步驟，請參閱[安裝 MABS 保護代理程式](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) 。

## <a name="create-a-protection-group-for-the-exchange-server"></a>建立 Exchange Server 的保護群組

1. 在 MABS 系統管理員主控台中，按一下 [保護]****，然後按一下工具功能區上的 [新增]****，以開啟 [建立新保護群組]**** 精靈。
2. 在嚮導**的歡迎**螢幕上,按下 **「下一步**」。
3. 在 **「選擇保護組類型」** 螢幕上,選擇 **「伺服器」** 並按**下 「下一步**」 。
4. 選取您想要保護的 Exchange Server 資料庫，然後按 [下一步] ****。

   > [!NOTE]
   > 如果您要保護 Exchange 2013，請檢查 [Exchange 2013 先決條件](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016)。
   >
   >

    下例中選取了Exchange 2010 資料庫。

    ![選擇群組成員](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. 選取資料保護方式。

    替保護群組命名，然後選取下列兩個選項：

   * 我想要使用磁碟進行短期保護。
   * 我想要線上保護。
6. 按 [下一步]  。
7. 如果您想要檢查 Exchange Server 資料庫的完整性，請選取 [執行 Eseutil 以檢查資料完整性] **** 選項。

    選擇此選項後,將在 MABS 上執行備份一致性檢查,以避免透過在 Exchange 伺服器上執行**esetil**命令產生的 I/O 流量。

   > [!NOTE]
   > 要使用此選項,必須將 Ese.dll 和 Eseutil.exe 檔案複製到 MABS 伺服器上的 C:_程式檔案\Microsoft Azure 備份_DPM_DPM_bin 目錄。 否則會觸發下列錯誤：  
   > ![eseutil 錯誤](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. 按 [下一步]  。
9. 選取用於 [複製備份]**** 的資料庫，然後按 [下一步]****。

   > [!NOTE]
   > 如果未為資料庫的至少一個 DAG 副本選擇「完全備份」,日誌將不會被截斷。
   >
   >
10. 設定 [短期備份]**** 的目標，然後按 [下一步]****。
11. 檢閱可用的磁碟空間，然後按 [下一步] ****。
12. 選擇 MABS 伺服器創建初始複製的時間,然後按下 **「下一步**」 。
13. 選取一致性檢查選項，然後按 [下一步] ****。
14. 選擇您要備份至 Azure 資料庫，然後按 [下一步] ****。 例如：

    ![指定線上保護資料](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. 定義 [Azure 備份]**** 的排程，然後按 [下一步]****。 例如：

    ![指定線上備份排程](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > 請注意，線上復原點是以快速完整復原點為基礎。 因此,您必須在為快速完全恢復點指定的時間之後安排連線恢復點。
    >
    >
16. 設定 [Azure 備份]**** 的保留原則，然後按 [下一步]****。
17. 選擇線上複寫選項並按 [下一步] ****。

    如果您有大型資料庫，則透過網路建立初始備份所需的時間很長。 若要避免這個問題，您可以建立離線備份。  

    ![指定線上保留期原則](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. 確認設定，然後按一下 [建立群組] ****。
19. 按一下 [關閉]  。

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
