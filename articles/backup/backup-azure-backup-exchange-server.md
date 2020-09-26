---
title: 透過 System Center DPM 備份 Exchange server
description: 了解如何使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ee89af311619922fa6ca585381d70ca66955f36a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271642"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份

本文說明如何設定 System Center 2012 R2 Data Protection Manager (DPM) 伺服器以將 Microsoft Exchange Server 備份至 Azure 備份。  

## <a name="updates"></a>更新

若要在 Azure 備份中成功註冊 DPM 伺服器，您必須安裝 System Center 2012 R2 DPM 的最新更新彙總套件和 Azure 備份代理程式的最新版本。 從 [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)取得最新的更新彙總套件。

> [!NOTE]
> 對於本文中的範例，會安裝 Azure 備份代理程式的 2.0.8719.0 版，而更新彙總套件 6 會安裝於 System Center 2012 R2 DPM。
>
>

## <a name="prerequisites"></a>Prerequisites

繼續之前，請確定符合使用 Microsoft Azure 備份保護工作負載的所有 [必要條件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 。 這些先決條件包含下列各項：

* 已在 Azure 網站上建立備份保存庫。
* 代理程式和保存庫認證已下載至 DPM 伺服器。
* 代理程式已安裝於 DPM 伺服器。
* 保存庫認證已用來註冊 DPM 伺服器。
* 如果您要保護 Exchange 2016，請升級至 DPM 2012 R2 UR9 或更新版本。

## <a name="dpm-protection-agent"></a>DPM 保護代理程式

若要在 Exchange Server 上安裝 DPM 保護代理程式，請遵循下列步驟：

1. 請確定已正確設定防火牆。 請參閱 [設定代理程式的防火牆例外狀況](/system-center/dpm/configure-firewall-settings-for-dpm)。
2. 在 DPM 管理主控台中選取 [ **管理 > 代理程式] > 安裝** ，以在 Exchange server 上安裝代理程式。 如需詳細步驟，請參閱 [安裝 DPM 保護代理程式](/system-center/dpm/deploy-dpm-protection-agent) 。

## <a name="create-a-protection-group-for-the-exchange-server"></a>建立 Exchange Server 的保護群組

1. 在 [DPM 管理主控台中，選取 [ **保護**]，然後在工具功能區上選取 [ **新增** ]，以開啟 [ **建立新保護組** 嚮導]。
2. 在嚮導的 [ **歡迎使用** ] 畫面上，選取 **[下一步]**。
3. 在 [ **選擇保護群組類型** ] 畫面上選取 [ **伺服器** ]，然後選取 **[下一步]**。
4. 選取您要保護的 Exchange server 資料庫，然後選取 **[下一步]**。

   > [!NOTE]
   > 如果您要保護 Exchange 2013，請檢查 [exchange 2013 必要條件](/system-center/dpm/back-up-exchange)。
   >
   >

    下例中選取了Exchange 2010 資料庫。

    ![選擇群組成員](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. 選取資料保護方式。

    替保護群組命名，然後選取下列兩個選項：

   * 我想要使用磁碟進行短期保護。
   * 我想要線上保護。
6. 選取 [下一步]  。
7. 如果您想要檢查 Exchange Server 資料庫的完整性，請選取 [執行 Eseutil 以檢查資料完整性] **** 選項。

    選取此選項之後，將會在 DPM 服務器上執行備份一致性檢查，以避免在 Exchange 伺服器上執行 **eseutil** 命令所產生的 i/o 流量。

   > [!NOTE]
   > 若要使用此選項，您必須將 Ese.dll 和 Eseutil.exe 檔案複製到 DPM 伺服器上的 C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin 目錄。 否則會觸發下列錯誤：  
   > ![eseutil 錯誤](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. 選取 [下一步]  。
9. 選取用於 **複本備份**的資料庫，然後選取 **[下一步]**。

   > [!NOTE]
   > 如果您未針對至少一個資料庫的 DAG 複本選取 [完整備份]，則不會截斷記錄。
   >
   >
10. 設定 **短期備份**的目標，然後選取 **[下一步]**。
11. 檢查可用的磁碟空間，然後選取 **[下一步]**。
12. 選取 DPM 服務器將建立初始複寫的時間，然後選取 [ **下一步]**。
13. 選取一致性檢查選項，然後選取 **[下一步]**。
14. 選擇您想要備份至 Azure 的資料庫，然後選取 [ **下一步]**。 例如：

    ![指定線上保護資料](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. 定義 **Azure 備份**的排程，然後選取 **[下一步]**。 例如：

    ![指定線上備份排程](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > 請注意，線上復原點是以快速完整復原點為基礎。 因此，您必須在針對快速完整復原點指定的時間之後，排定線上復原點。
    >
    >
16. 針對 **Azure 備份**設定保留原則，然後選取 **[下一步]**。
17. 選擇線上複寫選項，然後選取 **[下一步]**。

    如果您有大型資料庫，則透過網路建立初始備份所需的時間很長。 若要避免這個問題，您可以建立離線備份。  

    ![指定線上保留期原則](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. 確認設定，然後選取 [ **建立群組**]。
19. 選取 [關閉]。

## <a name="recover-the-exchange-database"></a>復原 Exchange 資料庫

1. 若要復原 Exchange 資料庫 **，請在 DPM 管理主控台中選取 [** 復原]。
2. 找出您要復原的 Exchange 資料庫。
3. 從「復原時間」 ** 下拉式清單選取線上復原點。
4. 選取 [復原]，以啟動 [**復原****嚮導]**。

線上復原點有五種復原類型：

* **復原到原始 Exchange Server 位置：** 資料將會還原到原始 Exchange Server。
* **復原到 Exchange Server 上的其他資料庫：** 資料將會還原到其他 Exchange Server 上的其他資料庫。
* **復原到復原資料庫：** 資料將會復原到 Exchange 復原資料庫 (RDB)。
* **複製到網路資料夾：** 資料將會還原到網路資料夾。
* **複製到磁帶：** 如果您有磁帶媒體櫃或獨立磁帶機連接並設定於 DPM 伺服器，則復原點將會複製到可用的磁帶。

    ![選擇線上複寫](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>後續步驟

* [Azure 備份常見問題集](backup-azure-backup-faq.md)
