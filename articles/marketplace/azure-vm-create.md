---
title: 在 Azure Marketplace 上建立虛擬機器供應專案。
description: 瞭解如何在 Microsoft 商業市場中建立虛擬機器供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 2ef80d26336ddbe3c015dfcde0c5ed29b762f39b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629711"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>如何在 Azure Marketplace 上建立虛擬機器供應專案

本文說明如何建立 [Azure Marketplace](https://azuremarketplace.microsoft.com/)的 Azure 虛擬機器供應專案。 其將同時處理 Windows 型和 Linux 型虛擬機器，其中包含作業系統、虛擬硬碟 (VHD)，以及多達 16 個資料磁碟。

開始之前，請 [在合作夥伴中心中建立商業 marketplace 帳戶](partner-center-portal/create-account.md)。 請確定您的帳戶已在商業 Marketplace 計畫中註冊。

## <a name="before-you-begin"></a>開始之前

如果您還沒有這麼做，請參閱 [規劃虛擬機器供應](marketplace-virtual-machines.md)專案。 它將說明虛擬機器的技術需求，並列出您在建立供應專案時所需的資訊和資產。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左窗格上，選取 [商業 Marketplace] > [概觀]。
3. 在 [ **總覽** ] 頁面上，選取 [ **+ 新增供應** 專案  >  **Azure 虛擬機器** ]。

    ![顯示左窗格功能表選項和 [新增供應項目] 按鈕的螢幕擷取畫面。](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 供應項目發佈之後，您在合作夥伴中心對其進行的任何編輯，只有在您重新發佈供應項目之後，才會出現在 Azure Marketplace 上。 變更供應項目之後，請務必一律重新發佈該供應項目。

輸入 **供應項目識別碼** 。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在 Azure Marketplace 供應項目和 Azure PowerShell 與 Azure CLI 的網址中看到此識別碼 (如果適用)。
- 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1** ，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入 **供應項目別名** 。 供應項目別名是用於合作夥伴中心內供應項目的名稱。

- Azure Marketplace 上不使用此名稱。 其與向客戶顯示的供應項目名稱和其他值不同。

選取 [建立] 以產生供應項目並繼續。 合作夥伴中心開啟 **供應專案設定** 頁面。

## <a name="enable-a-test-drive-optional"></a>啟用 (選用) 的試用產品

試用產品是為潛在客戶展示您的供應專案的絕佳方式，方法是讓他們能夠存取預先設定的環境一段固定的時數。 提供試用產品會產生更高的轉換率，並產生高度合格的潛在客戶。 若要深入瞭解試用產品，請參閱 [什麼是試用產品？](./what-is-test-drive.md)。

> [!TIP]
> 試用產品不同于免費試用版。 您可以提供試用產品、免費試用或兩者。 它們都能提供客戶一段固定時間的解決方案。 但是，試用產品也包含實際操作的自我引導式導覽，說明您產品的主要功能，以及在實際執行案例中所示範的優點。

若要啟用測試磁片磁碟機，請選取 [ **啟用試用** 產品] 核取方塊。 您稍後將會設定試用產品。 使用試用產品時，需要設定 CRM (請參閱下一節) 。

## <a name="configure-customer-leads-management"></a>設定客戶潛在客戶管理

當您使用合作夥伴中心將供應專案發佈至商用 marketplace 時，請將它連線到您的客戶關係管理 (CRM) 系統。 這可在有人對您的產品感興趣或加以使用時，讓您立即收到客戶連絡人資訊。 如果您想要啟用試用產品，則需要連接到 CRM (請參閱) 上一節。 否則，可以選擇連線到 CRM。

1. 在 [ **客戶潛在客戶** ] 下，選取 [連線 **]** 連結。
1. 在 [ **連接詳細資料** ] 對話方塊中，選取潛在客戶目的地。
1. 完成顯示的欄位。 如需詳細步驟，請參閱下列文章：

   - [設定供應項目以將潛在客戶傳送至 Azure 資料表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [設定您的供應專案，以將潛在客戶傳送給 dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (先前為 Dynamics CRM Online) 
   - [設定您的供應專案以將潛在客戶傳送至 HTTPS 端點](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [設定供應項目以將潛在客戶傳送至 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [設定您的供應項目，以便將潛在客戶傳送至 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要驗證您所提供的設定，請選取 [ **驗證** ] 連結。
1. 選取 [連接]  。

選取 [ **儲存草稿** ]，然後繼續前往左側導覽功能表中的下一個索引標籤 [ **屬性** ]。

## <a name="next-steps"></a>後續步驟

- [如何設定虛擬機器供應專案屬性](azure-vm-create-properties.md)
- [陳列供應項目的最佳做法](gtm-offer-listing-best-practices.md)