---
title: 管理 Windows 虛擬桌面的應用程式群組 (入口網站) - Azure
description: 如何使用 Azure 入口網站管理 Windows 虛擬桌面應用程式群組。
author: Heidilohr
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 21dba3c9beeefa2b25b8038f6cbd0ec77f947774
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226721"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>教學課程：使用 Azure 入口網站管理應用程式群組

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統)，請參閱[此文章](./virtual-desktop-fall-2019/manage-app-groups-2019.md)。

為新的 Windows 虛擬桌面主機集區建立的預設應用程式群組，也會發佈完整桌面。 此外，您可以為主機集區建立一或多個 RemoteApp 應用程式群組。 遵循本教學課程來建立 RemoteApp 應用程式群組及發佈個別 [開始] 功能表應用程式。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 RemoteApp 群組。
> * 授與 RemoteApp 程式的存取權。

## <a name="create-a-remoteapp-group"></a>建立 RemoteApp 群組

如果您已使用 Azure 入口網站或 PowerShell 建立主機集區和工作階段主機 VM，您可以透過下列程序，從 Azure 入口網站新增應用程式群組：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
   
   >![NOTE] 如果您要登入 US Gov 入口網站，請改為移至 [https://portal.azure.us/](https://portal.azure.us/)。

2.  搜尋並選取 **Windows 虛擬桌面**。

3. 您可以直接新增應用程式群組，也可以從現有的主機集區新增。 選擇下列選項：

    - 在頁面左側的功能表中選取 [應用程式群組]，然後選取 [+ 新增]。

    - 選取畫面左側功能表中的 [主機集區]、選取主機集區的名稱、從左側功能表中選取 [應用程式群組]，然後選取 [+ 新增]。 在此情況下，[基本資料] 索引標籤上會早已選取主機集區。

4. 在 [基本資料] 索引標籤上，選取您想要為其建立應用程式群組的 [訂用帳戶] 和 [資源群組]。 您也可以選擇建立新的資源群組，而不是選取現有的資源群組。

5. 從下拉式功能表中，選取將與應用程式群組相關聯的 [主機集區]。

    >[!NOTE]
    >您必須選取與應用程式群組相關聯的主機集區。 應用程式群組具有從工作階段主機提供服務的應用程式或桌面，而工作階段主機則是主機集區的一部分。 應用程式群組必須在建立期間與主機集區相關聯。

    > [!div class="mx-imgBorder"]
    > ![Azure 入口網站中 [基本資料] 索引標籤的螢幕擷取畫面。](media/basics-tab.png)

6. 在 [應用程式群組類型] 底下選取 [RemoteApp]，然後輸入 RemoteApp 的名稱。

      > [!div class="mx-imgBorder"]
      > ![應用程式群組類型欄位的螢幕擷取畫面。 [RemoteApp] 已醒目提示。](media/remoteapp-button.png)

7.  完成時，選取 [下一步:指派 >] 索引標籤。

8.  若要將個別使用者或使用者群組指派到應用程式群組，請選取 [+ 新增 Azure AD 使用者或使用者群組]。

9.  選取您想要讓其擁有應用程式存取權的使用者。 您可以選取一個或多個使用者和使用者群組。

     > [!div class="mx-imgBorder"]
     > ![使用者選取功能表的螢幕擷取畫面。](media/select-users.png)

10.  選取 [選取]  。

11.  完成時，選取 [下一步:應用程式 >]，然後選取 [+ 新增應用程式]。

12.  若要從 [開始] 功能表新增應用程式：

      - 在 [應用程式來源] 底下，從下拉式功能表中選取 [開始功能表]。 接下來，在 [應用程式] 底下，從下拉式功能表中選擇應用程式。

     > [!div class="mx-imgBorder"]
     > ![新增應用程式畫面的螢幕擷取畫面，其中已選取 [開始] 功能表。](media/add-app-start.png)

      - 在 [顯示名稱]  中，輸入使用者將在用戶端上看見的應用程式名稱。

      - 將其他選項保留為原狀，然後選取 [儲存]  。

13.  若要從特定檔案路徑新增應用程式：

      - 在 [應用程式來源] 底下，從下拉式功能表中選取 [檔案路徑]。

      - 在 [應用程式路徑] 中，輸入工作階段主機 (已向相關聯主機集區註冊) 上的應用程式路徑。

      - 在 [應用程式名稱]  、[顯示名稱]  、[圖示路徑]  和 [圖示索引]  欄位中輸入應用程式的詳細資料。

      - 選取 [儲存]  。

     > [!div class="mx-imgBorder"]
     > ![新增應用程式頁面的螢幕擷取畫面，其中已選取檔案路徑。](media/add-app-file.png)

14.  針對您要新增到應用程式群組的每個應用程式，重複執行此流程。

15.  接下來，選取 [下一步:工作區 >]。

16.  如果您想要將應用程式群組註冊到工作區，請針對 [註冊應用程式群組] 選取 [是]。 如果您想要在稍後註冊應用程式群組，請選取 [否]  。

17.  如果您選取 [是]  ，則可以選取現有的工作區來註冊應用程式群組。

       >[!NOTE]
       >您只能對建立在主機集區所在位置的工作區註冊應用程式群組。 此外， 如果您先前已從新應用程式群組所在的主機集區中，將其他應用程式群組註冊到工作區，則會選取相同工作區，而且您無法進行編輯。 主機集區中的所有應用程式群組都必須註冊到相同的工作區。

     > [!div class="mx-imgBorder"]
     > ![對已存在工作區註冊應用程式群組的頁面螢幕擷取畫面。 已預先選取主機集區。](media/register-existing.png)

18.  (選擇性) 如果您想要建立標籤以便能夠輕鬆組織工作區，請選取 [下一步:標籤 >]，然後輸入您的標籤名稱。

19.  當您完成時，選取 [檢閱 + 建立]。

20.  等待驗證程序完成。 完成時，請選取 [建立]  以部署您的應用程式群組。

部署程序會為您執行下列作業：

- 建立 RemoteApp 應用程式群組。
- 將選取的應用程式新增至應用程式群組。
- 將應用程式群組發佈給您選取的使用者和使用者群組。
- 註冊應用程式群組 (如果您選擇這麼做)。
- 根據您的設定建立 Azure Resource Manager 範本的連結，以便您稍後下載並儲存。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立應用程式群組、以 RemoteApps 程式填入群組，以及將使用者指派給應用程式群組。 若要深入了解如何建立驗證主機集區，請參閱下列教學課程。 您可以使用驗證主機集區來監視服務更新，再將其推展到生產環境。

> [!div class="nextstepaction"]
> [建立主機集區以驗證服務更新](./create-validation-host-pool.md)
