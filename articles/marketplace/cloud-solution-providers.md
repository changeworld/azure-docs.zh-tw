---
title: 雲端解決方案提供者 - Microsoft 商業市集
description: 了解如何透過商業市集中的 Microsoft 雲端解決方案提供者 (CSP) 計畫合作夥伴通道來銷售您的供應項目。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131168"
---
# <a name="cloud-solution-provider-program"></a>雲端解決方案提供者計畫

本文說明如何將您的供應項目設定為適用於雲端解決方案提供者 (CSP) 計畫。 除了透過 [商業 marketplace 線上商店](overview.md#commercial-marketplace-online-stores)發佈您的供應專案，您也可以透過 CSP 方案銷售，以觸及方案所提供的數百萬個合格 Microsoft 客戶。

您可以選擇是否要在 CSP 方案中設定新的或現有供應專案以供使用，這可讓 CSP 合作夥伴銷售您的產品，並為客戶建立配套的解決方案。

發行者要負責為終端客戶提供協助修正支援，並為 CSP 計畫中的合作夥伴和/或客戶提供一套機制，使其可與您連絡以取得支援。 最佳做法是為 CSP 計畫中的合作夥伴提供使用者文件、訓練及服務健康狀態/中斷通知 (如適用)，讓 CSP 計畫中的合作夥伴有能力處理來自客戶的第 1 層支援要求。  

下列供應項目有資格選擇加入由 CSP 計畫的合作夥伴進行銷售的行列：

- 軟體即服務 (SaaS) 交易供應項目
- 虛擬機器 (VM)
- 解決方案範本
- 受控應用程式

> [!NOTE]
> 容器和自備授權 (BYOL) VM 方案依預設會加入宣告至 CSP 方案中的合作夥伴銷售。

## <a name="how-to-configure-an-offer"></a>如何設定供應專案

當您在合作夥伴中心中建立供應專案時，請設定 CSP 方案加入宣告。 [深入了解如何變更發行者體驗](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>合作夥伴中心選擇加入

加入宣告的體驗位於 CSP 轉銷商的物件課程模組底下：

![CSP 轉銷商對象](media/marketplace-publishers-guide/csp-reseller-audience.png)

從三個選項中選擇：

1. CSP 方案中的任何合作夥伴。
2. 我選取的 CSP 方案中的特定合作夥伴。
3. CSP 方案中沒有任何合作夥伴。

#### <a name="option-1-any-partner-in-the-csp-program"></a>選項1： CSP 方案中的任何合作夥伴

![CSP 計畫中的任何合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 若選擇此選項，CSP 計畫中的所有合作夥伴都有資格將您的供應項目轉銷給其客戶。

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>選項2：我選取的 CSP 方案中的特定合作夥伴

![CSP 計畫中我選定的特定合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-two.png)

若選擇此選項，您會授權 CSP 計畫中有哪些合作夥伴有資格轉銷您的供應項目。

若要授權夥伴，請選取 [ **選取 CSP 合作夥伴** ]，接著會出現一個功能表，讓您依合作夥伴名稱或 CSP Azure Active Directory (Azure AD) 租使用者識別碼進行搜尋。

![選取 CSP 功能表](media/marketplace-publishers-guide/csp-pop-up-module.png)

您可以套用搜尋篩選條件，例如 **國家** 、 **專長認證** 或 **技能** 。

![用來搜尋合作夥伴的國家/地區、專長認證和技能篩選條件](media/marketplace-publishers-guide/csp-add-resellers.png)

選擇好合作夥伴清單後，請選取 [新增]。

![CSP 計畫中的授權合作夥伴清單範例](media/marketplace-publishers-guide/csp-add-resellers-details.png)

CSP 轉銷商的對象頁面上隨即會出現資料表，裡面會顯示您所選的合作夥伴清單。

![CSP 轉銷商對象頁面上有合作夥伴清單的資料表](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

選取 [儲存草稿] 以註冊您的變更。

如果此供應項目尚未發行，則必須發佈供應項目才能將其提供給選取的合作夥伴。

>[!NOTE]
>如果您在某個指定區域授權 CSP 計畫中的某個合作夥伴，其便可將供應項目銷售給屬於該特定區域的任何客戶。 如需有關如何在區域下分類 CSP 供應專案的詳細資訊，請參閱 [雲端解決方案提供者計畫區域市場與貨幣](/partner-center/regional-authorization-overview)。

如果您要更新已發佈供應項目的 CSP 清單，請新增其他合作夥伴，然後選取 [同步 CSP 對象]。

如果您的供應項目已有授權合作夥伴清單，而您想要讓其他供應項目使用同一份清單，請使用 **匯入/匯出** 。 瀏覽至具有 CSP 清單的供應項目，然後選取 [匯出 CSP]。 此功能會開發可供匯入至其他供應項目的 .csv 檔案。

#### <a name="option-3-no-partners-in-the-csp-program"></a>選項3： CSP 方案中沒有合作夥伴

![不提供給 CSP 計畫中的任何合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-three.png)

若選擇此選項，即表示您選擇不要讓供應項目加入 CSP 計畫。 您可以隨時變更此選擇。

## <a name="deauthorize-partners-in-the-csp-program"></a>將 CSP 計畫中的合作夥伴取消授權

如果您已授權 CSP 計畫中的合作夥伴，且該合作夥伴已將產品轉銷給客戶，您就不能將該合作夥伴取消授權。

如果 CSP 計畫中的合作夥伴尚未將您的產品銷售給客戶，而您想要在已將供應項目發佈後移除 CSP，請使用下列指示：

1. 移至 [[支援要求] 頁面](https://aka.ms/marketplacepublishersupport)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取項目。

2. 針對 [選取產品版本]，請選取 [上線供應項目管理]。
3. 針對 [選取最能說明問題的類別]，請選擇與您供應項目相關的類別。
4. 針對 [選取最能說明問題的問題]，請選取 [更新現有供應項目]。
5. 選取 [下一步] 以導向至可供輸入更多問題詳細資料的 [問題詳細資料] 頁面。
6. 使用 [將 CSP 取消授權] 作為問題的標題，並填寫其餘必要區段。

## <a name="navigate-between-options"></a>在選項之間瀏覽

使用此區段可在這三個 CSP 轉銷商選項之間瀏覽。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>從選項一瀏覽：CSP 計畫中的任何合作夥伴

如果您的供應項目目前為 **選項 1：CSP 計畫中的任何合作夥伴** ，而您想要瀏覽至其他兩個選項的其中一個，請使用下列指示來建立要求：

1. 移至 [[支援要求] 頁面](https://aka.ms/marketplacepublishersupport)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取項目。

2. 針對 [選取產品版本]，請選取 [上線供應項目管理]。
3. 針對 [選取最能說明問題的類別]，請選擇與您供應項目相關的類別。
4. 針對 [選取最能說明問題的問題]，請選取 [更新現有供應項目]。
5. 選取 [下一步] 以導向至可供輸入更多問題詳細資料的 [問題詳細資料] 頁面。
6. 使用 [將 CSP 取消授權] 作為問題的標題，並填寫其餘必要區段。

> [!NOTE]
> 如果您嘗試瀏覽至選項二，而且 CSP 計畫中已有合作夥伴將該供應項目轉銷給其客戶，則該合作夥伴依預設已在您的授權合作夥伴清單中。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>從選項二瀏覽：CSP 計畫中我選定的特定合作夥伴

如果您的供應項目目前為 **選項 2：CSP 計畫中我選定的特定合作夥伴** ，而您想要瀏覽至 **選項一：CSP 計畫中的任何合作夥伴** ，請使用下列指示來建立要求：

1. 移至 [[支援要求] 頁面](https://aka.ms/marketplacepublishersupport)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取項目。

2. 針對 [選取產品版本]，請選取 [上線供應項目管理]。
3. 針對 [選取最能說明問題的類別]，請選擇與您供應項目相關的類別。
4. 針對 [選取最能說明問題的問題]，請選取 [更新現有供應項目]。
5. 選取 [下一步] 以導向至可供輸入更多問題詳細資料的 [問題詳細資料] 頁面。
6. 使用 [將 CSP 取消授權] 作為問題的標題，並填寫其餘必要區段。

 如果您的供應項目目前為 **選項 2：CSP 計畫中我選定的特定合作夥伴** ，而您想要瀏覽至 **選項 3：不提供給 CSP 計畫中的任何合作夥伴** ，則必須在您先前授權的 CSP 計畫合作夥伴尚未轉銷您的供應項目給終端客戶時，才能瀏覽至該選項。 使用下列指示來建立要求：

1. 移至 [[支援要求] 頁面](https://aka.ms/marketplacepublishersupport)。 系統會自動為您填入前幾個下拉式功能表。

   > [!NOTE]
   > 請勿變更預先填入的下拉式功能表選取項目。

2. 針對 [選取產品版本]，請選取 [上線供應項目管理]。
3. 針對 [選取最能說明問題的類別]，請選擇與您供應項目相關的類別。
4. 針對 [選取最能說明問題的問題]，請選取 [更新現有供應項目]。
5. 選取 [下一步] 以導向至可供輸入更多問題詳細資料的 [問題詳細資料] 頁面。
6. 使用 [將 CSP 取消授權] 作為問題的標題，並填寫其餘必要區段。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>從選項 3 瀏覽：不提供給 CSP 計畫中的任何合作夥伴

如果您的供應項目目前為 **選項 3：不提供給 CSP 計畫中的任何合作夥伴** ，則您可以隨時瀏覽至其他兩個選項的其中一個。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>與 CSP 計畫合作夥伴共用銷售和支援材料

為了協助雲端解決方案提供者計畫中的合作夥伴最有效地表示您的供應專案，並與您的組織互動，您必須提交可提供給轉售商的銷售和支援材料。 這些資源將不會對線上商店中的客戶公開。

### <a name="partner-center-csp-channel"></a>合作夥伴中心 CSP 通道

如果您已選擇合作夥伴中心中的 CSP 通道，發行者必須在供應專案清單模組下輸入可裝載相關行銷資料和通道連絡人資訊的 URL。

![合作夥伴中心 CSP 宣傳材料資訊](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [進入市場服務](https://partner.microsoft.com/reach-customers/gtm)。
- 請登入[合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立及設定您的供應項目。