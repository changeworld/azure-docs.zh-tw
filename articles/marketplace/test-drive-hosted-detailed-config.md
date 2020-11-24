---
title: Azure Marketplace 中託管的試用產品的詳細設定
description: 說明商用市集中託管試用產品的設定步驟
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 9e8aecfe8b16d8af755d3f4c3c71b875d4e8ec40
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751244"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>託管測試磁片磁碟機的詳細設定

本文說明如何為 Dynamics 365 for Customer Engagement 或 Dynamics 365 for Operations 設定託管的試用產品。

## <a name="configure-for-dynamics-365-customer-engagement"></a>設定 Dynamics 365 Customer Engagement

1. 登入[合作夥伴中心](https://partner.microsoft.com/)。
2. 如果您無法存取上述連結，您需要在 [此](https://appsource.microsoft.com/partners/list-an-app) 提交要求以發佈您的應用程式。 一旦我們檢查要求，您就會獲得開始發佈程式的存取權。
3. 尋找現有 **的 dynamics 365 For Customer engagement 供應專案** ，或建立新的 **Dynamics 365 for Customer engagement 供應專案** 。
4. 選取 [ **啟用試用** 產品] 核取方塊，並選取 **試用** 產品的類型 (請參閱以下) 的專案符號，然後選取 [ **儲存**]。

    [![選取 [啟用試用產品] 核取方塊。](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **試用產品類型** -選擇 **Microsoft Hosted (Dynamics 365 for Customer Engagement & PowerApps)**。 這表示 Microsoft 將裝載及維護執行試用產品使用者布建和解除布建的服務。

5. 使用 [這些指示](https://docs.microsoft.com/azure/marketplace/test-drive-azure-subscription-setup)，授與 Microsoft AppSource 許可權，以在您的租使用者中布建及取消布建試用產品使用者。 在此步驟中，您將會產生 **AZURE AD APP 識別碼** 和 Azure AD App 以下所述的索引 **鍵值** 。
6. 在 [ **試用產品技術** 設定] 頁面上完成這些欄位。

    [![試用產品技術設定頁面。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **最大並行測試磁片磁碟機** –同時執行中的試用產品的並行使用者數目。 當使用者的試用產品為使用中時，每位使用者都會取用 Dynamics 授權，因此請確定您至少有此眾多 Dynamics 授權可供試用產品使用者使用。 建議使用3到5。
    - **試用產品持續時間** –使用者的試用產品將使用的時數。 時間到期之後，使用者將會從您的租使用者取消布建。 建議您根據應用程式的複雜度而定，2-24 小時。 如果使用者的時間已用盡，而且想要再次存取試用產品，使用者一律可以要求另一個試用產品。
    - **實例 url** –試用產品使用者啟動試用產品時將傳送至其中的 url。 這通常是您的應用程式和範例資料安裝所在的 Dynamics 365 實例 URL。 範例值： `https://testdrive.crm.dynamics.com` 。
    - **實例 WEB API url** – Dynamics 365 實例的 WEB api url。 登入您的 Microsoft Dynamics 365 實例，然後流覽至 **設定**  >  **自訂**  >  **開發人員資源**  >  **實例 Web API** ，並將位址 (URL) ，以抓取此值。 範例值：

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="實例 Web API 的範例。":::

    - **角色名稱** –您為試用產品建立的自訂 Dynamics 365 安全性角色的名稱，或者您可以使用現有的角色。 新角色應已新增至角色的最小必要許可權，才能登入 Customer Engagement 實例。 請參閱登 [入 Microsoft Dynamics 365 所需的最低許可權](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)。 這是在使用者的試用產品中，將指派給使用者的角色。 範例值： `testdriverole` 。
    
        > [!IMPORTANT]
        > 請確定未新增安全性群組檢查。 這可讓使用者同步處理至 Customer Engagement 實例。

    - **Azure Active Directory 租使用者識別碼** –適用于 Dynamics 365 實例的 Azure 租使用者識別碼。 若要取得此值，請登入 Azure 入口網站並流覽至 **Azure Active Directory**  >  **屬性**，並複製目錄識別碼。 範例值： 172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **Azure Active Directory 租使用者名稱** –適用于您 Dynamics 365 實例的 Azure 租使用者名稱。 請使用 `<tenantname>.onmicrosoft.com` 格式。 範例值： `testdrive.onmicrosoft.com` 。
    - **Azure Active Directory 應用程式識別碼** –您在步驟5中建立之 AZURE ACTIVE DIRECTORY (AD) 應用程式的識別碼。 範例值： `53852862-a2ae-4e43-9461-faa49650a096` 。
    - **Azure Active Directory 應用程式用戶端密碼** –在步驟5中建立之 Azure AD 應用程式的秘密。 範例值： `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` 。

7. 提供 marketplace 清單詳細資料。 選取 **語言** 以查看進一步的必要欄位。

    [![Marketplace 清單詳細資料頁面。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **描述** -您的試用產品總覽。 布建試用產品時，系統會對使用者顯示此文字。 此欄位支援 HTML，因此您可以提供格式豐富的內容。
    - **使用者手冊** –一種 PDF 使用者手冊，可協助試用使用者瞭解如何使用您的應用程式。
    - **試用產品示範影片** -展示應用程式 (選用) 的影片。

## <a name="configure-for-dynamics-365-operations"></a>設定 Dynamics 365 作業

2. 如果您無法存取上述連結，您需要在 [此](https://appsource.microsoft.com/partners/list-an-app) 提交要求以發佈您的應用程式。 一旦我們檢查要求，您就會獲得開始發佈程式的存取權。
3. 尋找 **適用于作業** 供應專案的現有 dynamics 365，或建立新的 **Dynamics 365 for operations** 供應專案。
4. 選取 [ **啟用試用** 產品] 核取方塊，並選取 **試用** 產品的類型 (請參閱以下) 的專案符號，然後選取 [ **儲存**]。

    [![選取 [啟用試用產品] 核取方塊。](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **試用產品類型** -選擇 **Dynamics 365 for Operations** 選項。 這表示 Microsoft 將裝載及維護執行試用產品使用者布建和解除布建的服務。

5. 使用 [這些指示](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)，授與 Microsoft AppSource 許可權，以在您的租使用者中布建及取消布建試用產品使用者。 在此步驟中，您將會產生 **AZURE AD APP 識別碼** 和 Azure AD App 以下所述的索引 **鍵值** 。
6. 在 [ **試用產品技術** 設定] 頁面上完成這些欄位。

    [![Marketplace 技術配置頁面。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **最大並行測試磁片磁碟機** –同時執行中的試用產品的並行使用者數目。 當使用者的試用產品為使用中時，每位使用者都會取用 Dynamics 授權，因此請確定您至少有此眾多 Dynamics 授權可供試用產品使用者使用。 建議使用3到5。
    - **試用產品持續時間** –使用者的試用產品將使用的時數。 時間到期之後，使用者將會從您的租使用者取消布建。 建議您根據應用程式的複雜度而定，2-24 小時。 如果使用者的時間已用盡，而且想要再次存取試用產品，使用者一律可以要求另一個試用產品。
    - **實例 url** –試用產品使用者啟動試用產品時將傳送至其中的 url。 這通常是您的應用程式和範例資料安裝所在的 Dynamics 365 實例 URL。 範例值： `https://testdrive.crm.dynamics.com` 。
    - **Azure Active Directory 租使用者識別碼** –適用于 Dynamics 365 實例的 Azure 租使用者識別碼。 若要取得此值，請登入 Azure 入口網站並流覽至 **Azure Active Directory**  >  **屬性**，並複製目錄識別碼。 範例值： 172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **Azure Active Directory 租使用者名稱** –適用于您 Dynamics 365 實例的 Azure 租使用者名稱。 請使用 `<tenantname>.onmicrosoft.com` 格式。 範例值： `testdrive.onmicrosoft.com` 。
    - **Azure Active Directory 應用程式識別碼** –您在步驟5中建立之 AZURE ACTIVE DIRECTORY (AD) 應用程式的識別碼。 範例值： `53852862-a2ae-4e43-9461-faa49650a096` 。
    - **Azure Active Directory 應用程式用戶端密碼** –在步驟5中建立之 Azure AD 應用程式的秘密。 範例值： `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` 。
    - **試用法律實體** –提供法律實體以指派試用版使用者。 您可以在 [建立或修改法律實體](https://technet.microsoft.com/library/hh242184.aspx)時建立一個新的。
    - **角色名稱** ：在您為試用產品建立的自訂 Dynamics 365 安全性角色中， (應用程式物件樹狀) 結構的 AOT 名稱。 這是在使用者的試用產品中，將指派給使用者的角色。

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="[安全性設定] 頁面。":::

7. 提供 marketplace 清單詳細資料。 選取 **語言** 以查看進一步的必要欄位。

    [![Marketplace 清單詳細資料頁面。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **描述** -您的試用產品總覽。 布建試用產品時，系統會對使用者顯示此文字。 此欄位支援 HTML，因此您可以提供格式豐富的內容。
    - **使用者手冊** –一種 PDF 使用者手冊，可協助試用使用者瞭解如何使用您的應用程式。
    - **試用產品示範影片** -展示應用程式 (選用) 的影片。

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
