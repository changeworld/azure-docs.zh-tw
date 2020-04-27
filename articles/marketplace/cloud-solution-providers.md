---
title: 雲端解決方案提供者 |Azure Marketplace
description: 發行者現在可以透過 Microsoft Cloud 解決方案提供者（CSP）合作夥伴通道來銷售其供應專案。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: b962610c585df288a9cb3297ed8e09c8abc5ac0a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160642"
---
# <a name="cloud-solution-providers"></a>雲端解決方案提供者

軟體供應專案可與雲端解決方案提供者（CSP）方案中的合作夥伴提供的數百萬個合格 Microsoft 客戶聯繫，以及透過[Microsoft web 店面](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)公開提供的供應專案。

發行者會以選擇性的方式，針對新的供應專案或現有的產品，在 CSP 方案中設定可用性，讓合作夥伴能夠銷售您的產品，並為客戶建立配套的解決方案。

發行者會負責為客戶提供協助修正支援，並為 CSP 計畫中的合作夥伴和/或客戶提供一套機制，以與您聯繫以取得支援。 最佳做法是在 CSP 計畫中提供合作夥伴與使用者檔、訓練及服務健康狀態/中斷通知（如適用），讓 CSP 計畫中的合作夥伴能夠處理來自客戶的第1層支援要求。  

下列供應專案有資格在 CSP 方案中加入宣告以由合作夥伴銷售：

- 軟體即服務（SaaS）交易提供
- 虛擬機器 (VM)
- 解決方案範本
- 受控應用程式

> [!NOTE]
> 根據預設，容器和自備授權（BYOL） VM Sku 已選擇要由 CSP 方案中的合作夥伴銷售。

## <a name="how-to-configure-an-offering"></a>如何設定供應專案

[CSP 方案加入宣告] 設定是在合作夥伴中心或 Cloud Partner 入口網站供應專案建立體驗中設定。 [深入瞭解變更的發行者體驗](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>合作夥伴中心加入宣告

在合作夥伴中心，您可以在 CSP 轉銷商物件課程模組底下找到加入宣告體驗。

![CSP 轉銷商物件](media/marketplace-publishers-guide/csp-reseller-audience.png)

在 CSP 轉銷商的觀眾課程模組中，您有三個選項可供選擇：

- 選項一： CSP 計畫中的任何合作夥伴
- 選項二：我選取 CSP 方案中的特定合作夥伴
- 選項3： CSP 計畫中沒有合作夥伴

#### <a name="option-one-any-partner-in-the-csp-program"></a>選項一： CSP 計畫中的任何合作夥伴

![CSP 計畫中的任何合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 藉由選擇此選項，CSP 方案中的所有合作夥伴都有資格將您的供應專案轉售給客戶。

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>選項二：我選取 CSP 方案中的特定合作夥伴

![我選取的 CSP 方案中的特定合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-two.png)

藉由選擇此選項，您可以授權 CSP 方案中的哪些合作夥伴有資格轉售您的供應專案。

若要授權合作夥伴，請按一下 [**選取 CSP 合作夥伴**]，此時會出現一個功能表，可讓您依合作夥伴名稱或 CSP AZURE ACTIVE DIRECTORY （AAD）租使用者識別碼進行搜尋。

![選取 CSP 功能表](media/marketplace-publishers-guide/csp-pop-up-module.png)

您可以套用搜尋篩選準則，例如**國家/地區**、專長**認證**或**技能**。

![合作夥伴搜尋的國家/地區、專長認證和技能篩選](media/marketplace-publishers-guide/csp-add-resellers.png)

選擇夥伴清單之後，請選取 [**新增**]。

![CSP 計畫中授權合作夥伴的範例清單](media/marketplace-publishers-guide/csp-add-resellers-details.png)

顯示您所選合作夥伴清單的表格會出現在 CSP 轉銷商的 [觀眾] 頁面上。

![具有 CSP 轉銷商物件頁面上合作夥伴清單的表格](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

選取 [**儲存草稿**] 以註冊您的變更。

如果這是未發行的供應專案，您將需要發佈您的供應專案，讓您選取的合作夥伴可以使用。

>[!NOTE]
>如果您在指定的區域中授權某個合作夥伴在 CSP 計畫中，他們可以向屬於該特定區域的任何客戶銷售供應專案。 如需有關 CSP 供應專案如何分類于區域的詳細資訊，請參閱[雲端解決方案提供者方案的區域市場和貨幣](https://docs.microsoft.com/partner-center/regional-authorization-overview)。

如果您要更新已發佈供應專案的 CSP 清單，請新增其他合作夥伴，然後選取 [**同步 CSP 物件**]。

如果您的供應專案已有授權合作夥伴的清單，而您想要對其他供應專案使用相同的清單，請使用匯**入/匯出**。 流覽至具有 CSP 清單的供應專案，然後選取 [**匯出 csp**]。 函式會開發可匯入至另一個供應專案的 .csv 檔案。

#### <a name="option-three-no-partners-in-the-csp-program"></a>選項3： CSP 計畫中沒有合作夥伴

![CSP 計畫中沒有任何合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-three.png)

選擇此選項，即會將您的供應專案從 CSP 計畫中退出。 您可以隨時變更此選取範圍。

### <a name="cloud-partner-portal-opt-in"></a>Cloud Partner 入口網站加入宣告

在 Cloud Partner 入口網站中，[Marketplace] 或 [店面] 索引標籤上會設定加入宣告。只有在合作夥伴中心才提供選擇 CSP 方案中特定合作夥伴的功能。

![CPP 中的 CSP 加入宣告體驗](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Deauthorize CSP 計畫中的合作夥伴

如果您已在 CSP 計畫中授權合作夥伴，且該合作夥伴已將產品轉售給客戶，則不允許您 deauthorize 該合作夥伴。

如果 CSP 方案中的合作夥伴尚未將您的產品銷售給客戶，而您想要在發佈供應專案之後移除 CSP，請使用下列指示：

1. 移至 [[支援要求] 頁面](https://partner.microsoft.com/support/v2/?stage=1)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取專案。

2. 針對 **[選取產品版本**]，選取 [ **Live 供應專案管理**]。
3. 針對 [**選取最能描述問題的類別**]，選擇參照您供應專案的類別。
4. 針對 **[選取最能描述問題的問題**]，選取 [**更新現有的供應**專案]。
5. 選取 **[下一步]** 以導向至 [**問題詳細資料] 頁面**，以輸入有關問題的更多詳細資料。
6. 使用**DEAUTHORIZE CSP**作為問題標題，並填寫其餘必要區段。




## <a name="navigate-between-options"></a>在選項之間流覽

使用此區段可在三個 CSP 轉銷商選項之間流覽。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>流覽選項一： CSP 計畫中的任何合作夥伴

如果您的供應專案目前為**選項1： CSP 計畫中的任何合作夥伴**，而您想要流覽至其他兩個選項的其中一個，請使用下列指示來建立要求：

1. 移至 [[支援要求] 頁面](https://partner.microsoft.com/support/v2/?stage=1)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取專案。

2. 針對 **[選取產品版本**]，選取 [ **Live 供應專案管理**]。
3. 針對 [**選取最能描述問題的類別**]，選擇參照您供應專案的類別。
4. 針對 **[選取最能描述問題的問題**]，選取 [**更新現有的供應**專案]。
5. 選取 **[下一步]** 以導向至 [**問題詳細資料] 頁面**，以輸入有關問題的更多詳細資料。
6. 使用**DEAUTHORIZE CSP**作為問題標題，並填寫其餘必要區段。

> [!NOTE]
> 如果您嘗試流覽至第二個選項，且 CSP 方案中的合作夥伴已轉售其客戶的供應專案，則該合作夥伴預設已在您的授權合作夥伴清單中。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>流覽第二個選項：我選取的 CSP 方案中的特定合作夥伴

如果您的供應專案目前為**選項2：我選取 csp 方案中的特定合作夥伴**，而您想要流覽至**選項1： csp 方案中的任何合作夥伴**，請使用下列指示來建立要求：

1. 移至 [[支援要求] 頁面](https://partner.microsoft.com/support/v2/?stage=1)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取專案。

2. 針對 **[選取產品版本**]，選取 [ **Live 供應專案管理**]。
3. 針對 [**選取最能描述問題的類別**]，選擇參照您供應專案的類別。
4. 針對 **[選取最能描述問題的問題**]，選取 [**更新現有的供應**專案]。
5. 選取 **[下一步]** 以導向至 [**問題詳細資料] 頁面**，以輸入有關問題的更多詳細資料。
6. 使用**DEAUTHORIZE CSP**作為問題標題，並填寫其餘必要區段。

 如果您的供應專案目前為**選項2：我選擇 csp 方案中的特定合作夥伴**，而您想要流覽至 [**選項3： Csp 計畫中沒有合作夥伴**]，只有在您先前授權的 csp 計畫中，您的合作夥伴未轉售您的供應專案給終端客戶時，才能夠流覽至該選項。 請使用下列指示來建立要求：

1. 移至 [[支援要求] 頁面](https://partner.microsoft.com/support/v2/?stage=1)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取專案。

2. 針對 **[選取產品版本**]，選取 [ **Live 供應專案管理**]。
3. 針對 [**選取最能描述問題的類別**]，選擇參照您供應專案的類別。
4. 針對 **[選取最能描述問題的問題**]，選取 [**更新現有的供應**專案]。
5. 選取 **[下一步]** 以導向至 [**問題詳細資料] 頁面**，以輸入有關問題的更多詳細資料。
6. 使用**DEAUTHORIZE CSP**作為問題標題，並填寫其餘必要區段。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>流覽選項3： CSP 計畫中沒有合作夥伴

如果您的供應專案目前為**選項3： CSP 方案中沒有合作夥伴**，您可以隨時流覽至其他兩個選項的其中一個。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>在 CSP 計畫中與合作夥伴共用銷售和支援資料

為了協助雲端解決方案提供者方案中的合作夥伴最有效地代表您的供應專案，並與您的組織互動，您必須提交可供轉銷商使用的銷售和支援材料。 這些資源不會公開給 marketplace 店面中的客戶。

### <a name="partner-center-csp-channel"></a>合作夥伴中心 CSP 頻道

如果您已加入宣告合作夥伴中心內的 CSP 通道，發行者必須在供應專案清單模組底下，輸入將相關行銷資料和通道連絡人資訊裝載于 CSP 通道的 URL：

![合作夥伴中心 CSP 的宣傳材料資訊](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Cloud Partner 入口網站 CSP 通道

如果您已選擇 Cloud Partner 入口網站中的 CSP 通道，發行者必須輸入 URL，以將相關的行銷資料和通道連絡人資訊裝載至 CSP 通道：

![Cloud Partner 入口網站 CSP 的宣傳材料資訊](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>後續步驟

請造訪[Azure Marketplace 和 AppSource 發行者指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

若要深入瞭解 marketplace 轉型服務，請參閱[進入市場服務](https://partner.microsoft.com/reach-customers/gtm)。

登入[合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以建立及設定您的供應專案。
