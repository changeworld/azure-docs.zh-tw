---
title: 透過 System Center DPM 備份 Exchange server
description: 了解如何使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b45efa0623edbec47b8ae12d3a97b1e032626530
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80396412"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份

本文說明如何設定 System Center 2012 R2 Data Protection Manager (DPM) 伺服器以將 Microsoft Exchange Server 備份至 Azure 備份。  

## <a name="updates"></a>更新

若要在 Azure 備份中成功註冊 DPM 伺服器，您必須安裝 System Center 2012 R2 DPM 的最新更新彙總套件和 Azure 備份代理程式的最新版本。 從 [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)取得最新的更新彙總套件。

> [!NOTE]
> 對於本文中的範例，會安裝 Azure 備份代理程式的 2.0.8719.0 版，而更新彙總套件 6 會安裝於 System Center 2012 R2 DPM。
>
>

## <a name="prerequisites"></a>先決條件

繼續之前，請確定符合使用 Microsoft Azure 備份保護工作負載的所有 [必要條件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 。 這些先決條件包含下列各項：

* 已在 Azure 網站上建立備份保存庫。
* 代理程式和保存庫認證已下載至 DPM 伺服器。
* 代理程式已安裝於 DPM 伺服器。
* 保存庫認證已用來註冊 DPM 伺服器。
* 如果您要保護 Exchange 2016，請升級為 DPM 2012 R2 UR9 或更新版本

## <a name="dpm-protection-agent"></a>DPM 保護代理程式

若要在 Exchange Server 上安裝 DPM 保護代理程式，請遵循下列步驟：

1. 請確定已正確設定防火牆。 請參閱 [設定代理程式的防火牆例外狀況](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019)。
2. 按一下 DPM 系統管理員主控台中的 [管理] > [代理程式] > [安裝]****，在 Exchange Server 上安裝代理程式。 如需詳細步驟，請參閱 [安裝 DPM 保護代理程式](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) 。

## <a name="create-a-protection-group-for-the-exchange-server"></a>建立 Exchange Server 的保護群組

1. 在 DPM 系統管理員主控台中，按一下 [保護]****，然後按一下工具功能區上的 [新增]****，以開啟 [建立新保護群組]**** 精靈。
2. 在嚮導的**歡迎**畫面上，按 **[下一步]**。
3. 在 [**選取保護群組類型**] 畫面上，選取 [**伺服器**] 並按 **[下一步]**。
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

    選取此選項之後，將會在 DPM 服務器上執行備份一致性檢查，以避免在 Exchange server 上執行**eseutil**命令所產生的 i/o 流量。

   > [!NOTE]
   > 若要使用此選項，您必須將 Ese.dll 和 Eseutil.exe 檔案複製到 DPM 伺服器上的 C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin 目錄。 否則會觸發下列錯誤：  
   > ![eseutil 錯誤](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. 按 [下一步]  。
9. 選取用於 [複製備份]**** 的資料庫，然後按 [下一步]****。

   > [!NOTE]
   > 如果您未針對資料庫的至少一個 DAG 複本選取「完整備份」，則不會截斷記錄。
   >
   >
10. 設定 [短期備份]**** 的目標，然後按 [下一步]****。
11. 檢閱可用的磁碟空間，然後按 [下一步] ****。
12. 選取 DPM 伺服器將建立初始複寫的時間，然後按 [下一步] ****。
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
19. 按一下 **關閉**。

## <a name="recover-the-exchange-database"></a>復原 Exchange 資料庫

1. 若要復原 Exchange 資料庫，請按一下 DPM 系統管理員主控台中的 [復原] **** 。
2. 找出您要復原的 Exchange 資料庫。
3. 從「復原時間」 ** 下拉式清單選取線上復原點。
4. 按一下 [復原]**** 啟動 [復原精靈]****。

線上復原點有五種復原類型：

* **復原到原始 Exchange Server 位置：** 資料將會還原到原始 Exchange Server。
* **復原到 Exchange Server 上的其他資料庫：** 資料將會還原到其他 Exchange Server 上的其他資料庫。
* **復原到復原資料庫：** 資料將會復原到 Exchange 復原資料庫 (RDB)。
* **複製到網路資料夾：** 資料將會還原到網路資料夾。
* **複製到磁帶：** 如果您有磁帶媒體櫃或獨立磁帶機連接並設定於 DPM 伺服器，則復原點將會複製到可用的磁帶。

    ![選擇線上複寫](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>後續步驟

* [Azure 備份常見問題集](backup-azure-backup-faq.md)
