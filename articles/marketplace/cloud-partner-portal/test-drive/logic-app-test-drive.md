---
title: 邏輯應用程式試用產品 |Azure Marketplace
description: 說明如何建置與 Dynamics AX/CRM 執行個體或 Azure 以外的其他任何資源連接的試用產品。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278377"
---
<a name="logic-app-test-drive"></a>邏輯應用程式試用產品
====================

此文章適用於在 AppSource 上有其供應項目，且想要建置與 Dynamics AX/CRM 執行個體或 Azure 以外的其他任何資源連接之試用產品的發佈者。

<a name="how-to-build-a-logic-app-test-drive"></a>如何建置邏輯應用程式試用產品
-----------------------------------

邏輯應用程式試用產品的試用產品檔目前仍在 GitHub 上進行[作業](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客戶參與](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)，請前往該處閱讀更多。

<a name="how-to-publish-a-test-drive"></a>如何發佈試用產品
---------------------------

既然您已經建置您的試用產品，此節逐步解說成功發佈試用產品所需的每個欄位。

![啟用試用產品功能](./media/azure-resource-manager-test-drive/howtopub1.png)

第一個和最重要的欄位是切換是否要顯示所有必要欄位的表單測試，以供您填寫。當您選取 [否] 時 **，** 表單會變成停用狀態，如果您在已停用試用產品的情況下重新發佈，就會從生產環境中移除

*注意*：如果有任何試用產品仍由使用者使用中，那些試用產品將會繼續執行，直到其工作階段到期為止。

### <a name="details"></a>詳細資料

要填寫的下一個區段是有關您試用產品供應項目的詳細資料。

![試用產品詳細資料](./media/azure-resource-manager-test-drive/howtopub2.png)

**描述 -** *[必填欄位]* 這是撰寫有關您試用產品所提供主要功能描述的位置。 客戶將到這裡查看您的試用產品涵蓋哪些產品的案例。 

**使用者手冊 -** *[必填欄位]* 這是您試用產品體驗的深入逐步解說。 客戶將開啟此部分，而且可以逐步查看您在其整個試用產品評估過程中要他們做的事。 此內容必須容易理解並遵循！ (必須是 .pdf 檔案)

**試用產品示範影片 -** \[建議\] 類似使用者手冊，最好能包括有關您試用產品體驗的影片教學課程。 客戶將在進行試用產品評估前或進行試用產品評估期間觀看影片，而且可以 逐步查看您在其整個試用產品評估過程中要他們做的事。 此內容必須容易理解並遵循！

- **名稱** - 您的影片標題
- **連結** - 必須是來自您的 Tube 或 Vimeo 的內嵌 URL。 有關如何取得內嵌 URL 的範例如下：
- **縮圖** - 必須是高品質影像 (533x324) 像素。 建議您在這裡擷取您試用產品體驗部分的一些螢幕擷取畫面。

下面顯示這些欄位如何在客戶的試用產品體驗期間顯示。

![試用產品欄位的外觀與風格](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>技術設定

要填寫的下一個區段是您設定試用產品邏輯應用程式的位置，並特別定義您的試用產品執行個體如何運作。

![試用產品技術設定](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **區域** - *[必要欄位]* 您選取的區域是您挑選試用產品邏輯應用程式資源部署所在的位置。

    *注意：* 如果邏輯應用程式有任何儲存在區域中的自訂資源，請務必在此處選取該區域。 若要執行此操作，最好是**在入口網站中的 Azure 訂用帳戶上，從本機完全部署邏輯應用程式，並確認可以運作**，然後在這裡將其寫入。

- **並行試用** - 產品的最大 *[必要欄位]* 已部署的試用產品實例數目，並在每個選取的區域等待存取。 客戶可以立即存取此試用產品，而不需要等候部署。

    *注意：* 如果您所經營的是您希望所有 N 個學員都採用試用產品的網路研討會/班級，則建議使用 N 個待命執行個體發佈，然後在課程結束後，重新發佈回您正常的待命執行個體數目。

- **試用產品持續時間 (小時) -** *[必填欄位]* 試用產品將維持可用的持續時間，以 \# 小時數表示。 在此持續時間過後，試用產品將會自動終止。

- **Azure 資源群組名稱 -** *[必填欄位]* 在您的邏輯應用程式試用產品儲存位置寫入資源群組名稱。

- **指派邏輯應用程式名稱 -** *[必填欄位]* 在客戶取得試用產品之前，寫入用來指派其中使用者的邏輯應用程式，在此寫入該邏輯應用程式的名稱。 請確定此檔案會儲存在上述的資源群組中。

- **取消佈建邏輯應用程式名稱 -** *[必填欄位]* 寫入您解除佈建在試用產品中建立的所有資源的邏輯應用程式名稱。 請確定此檔案會儲存在上述的資源群組中。

- **存取資訊 -** *[必填欄位]* 客戶取得其試用產品之後，會向他們顯示存取資訊。 這些指示旨在共用來自您試用產品 Resource Manager 範本的實用輸出參數。 若要包括輸出參數，請使用雙大括弧 (例如 **{{outputname}}**)，而且它們將會被正確插入該位置。 (這裡建議 HTML 字串格式，以便在前端轉譯)。

### <a name="test-drive-deployment-subscription-details"></a>試用產品部署訂用帳戶詳細資料

要填寫的最後一個區段是要能夠透過連結您的 Azure 訂用帳戶與 Azure Active Directory (AD) 以自動部署試用產品。

![試用產品部署訂用帳戶詳細資料](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure 訂用帳戶識別碼 -** *[必填欄位]* 這會授與對 Azure 服務與 Azure 入口網站的存取權。 訂用帳戶是回報資源使用狀況並針對所使用服務計費的位置。 如果您還沒有僅用於試用產品的**獨立** Azure 訂用帳戶，請建立一個獨立訂用帳戶。 您可以透過登入 Azure 入口網站並瀏覽到左側功能表中的 [訂用帳戶] 以尋找 Azure 訂用帳戶識別碼。
(範例："a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure 訂用帳戶](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD 租用戶識別碼 -** *[必填欄位]* 如果您已經有可用的租用戶識別碼，可以在下面的 [屬性 -\> 目錄識別碼] 中找到它。

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

否則，請在 Azure Active Directory 中建立新的租用戶。

![Azure Active Directory 屬性畫面](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Azure Active Directory 租用戶](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App 識別碼 -** *[必填欄位]* 下一個步驟是建立並註冊新的應用程式。 我們將使用此應用程式在您的試用產品執行個體上執行作業。

1. 瀏覽到新建立的目錄或現有的目錄，並在篩選窗格中選取 Azure Active Directory。
2. 搜尋「應用程式註冊」並按一下 [新增]
3. 提供應用程式名稱。
4. 在 [類型] 中選取 [Web 應用程式/API]
5. 在 [登入 URL] 中提供任意值，我們將不會使用該欄位。
6. 按一下 [建立]。
7. 建立應用程式之後，移至 [屬性 -\> 將應用程式設定為多租用戶]，然後按一下 [儲存]。

按一下 [儲存]。 最後一個步驟是擷取這個已註冊之應用程式的應用程式識別碼，並將它貼入到這裡的 [試用產品] 欄位。

![Azure Active Directory 應用程式識別碼](./media/azure-resource-manager-test-drive/subdetails7.png)

假設我們使用應用程式部署到訂用帳戶，我們必須在訂用帳戶上將應用程式新增為參與者。 有關這些動作的指示如下：

1. 瀏覽到 [訂用帳戶] 刀鋒視窗，並選取您僅用於試用產品的適當訂用帳戶。
1. 按一下 **[存取控制（IAM）**]。
1. 按一下 [**角色指派**] 索引標籤。 ![Azure Active Directory，加入新的存取控制主體](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. 按一下 [**新增角色指派**]。
1. 將角色設定為 [**參與者**]。
1. 輸入 Azure AD 應用程式的名稱，並選取應用程式以指派角色。
    ![Azure Active Directory 權限](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. 按一下 **[儲存]** 。

**Azure AD App 金鑰 -** *[必填欄位]* 最後一個欄位是要產生驗證金鑰。 在 [金鑰] 下，新增 [金鑰描述] 並將期間設定為永不到期，然後選取 [儲存]。 **務必**避免擁有已到期的金鑰，這將會使得生產環境中的試用產品中斷。 複製此值並將它貼到您的必填 [試用產品] 欄位中。

![Azure Active Directory 金鑰區段](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> 您無法使用 Azure App 註冊預覽，因為它目前不會產生 base64 編碼的金鑰。


<a name="next-steps"></a>後續步驟
----------

既然您已經填寫所有試用產品欄位，請瀏覽一遍，然後**重新發佈**您的供應項目。 一旦您的試用產品通過認證程序，您應該在您供應項目的**預覽**中全面測試客戶體驗。 在 UI 中啟動試用產品，並確認您的試用產品正確地完整部署。

請務必記住，您不需要刪除試用產品的任何部分，因為它們是針對您的客戶所佈建，因此在客戶完成評估之後，試用產品服務將會自動清除這些資源群組。

一旦您對預覽供應專案感到滿意之後，就可以開始**上線**！ 供應項目發佈之後，必須由 Microsoft 進行審核程序，以便再次檢查整個端對端體驗。 如果供應項目因某個原因遭拒，我們將會傳送通知給工程連絡人，說明您必須如何修正您的供應項目。

如果您有其他問題、在尋找疑難排解建議，或想讓試用產品更成功，請移至[常見問題集、疑難排解與最佳做法](./marketing-and-best-practices.md)。
