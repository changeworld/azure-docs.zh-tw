---
title: 在合作夥伴中心中取得商用 marketplace 入口網站的支援
description: 瞭解合作夥伴中心中的支援選項，包括如何提出支援要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/14/2020
ms.openlocfilehash: 8979a94ee63ef50c0a3abacd5ab3515226200131
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183433"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>合作夥伴中心中的商業 marketplace 方案支援

Microsoft 提供各式各樣的產品和服務支援。 尋找正確的支援小組很重要，可確保適當且及時的回應。 請考慮下列案例，這些案例應有助於將您的查詢傳送給適當的團隊：

- 如果您是發行者，並有來自客戶的問題，請要求您的客戶使用 [Azure 入口網站](https://portal.azure.com/)中的支援連結來要求支援。
- 如果您是發行者，而且偵測到在 Azure 上執行的應用程式有安全性問題，請參閱 [如何記錄安全性事件支援票證](/azure/security/fundamentals/event-support-ticket)。 發行者一旦發現可疑的安全性事件，必須立即報告，包括其 Azure Marketplace 軟體和服務供應項目的安全性事件和弱點。
- 如果您是發行者，並且有與您的應用程式或服務相關的問題，請參閱下列支援選項。

## <a name="support-options-for-publishers"></a>發行者的支援選項

1. 使用您的工作帳戶登入 [合作夥伴中心上的商用 marketplace 方案](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) (如果您尚未這麼做，您將需要 [建立合作夥伴中心帳戶](partner-center-portal/create-account.md)。

2. 在頁面右側的上方功能表中，選取 [ **支援** ] 圖示。

3. [說明 **及支援** ] 窗格會出現在頁面的右側。

   ![支援下拉式功能表](./media/support/commercial-marketplace-support-pane.png)

    或移至 **首頁** 窗格，然後選取 [說明 **及支援**]。

   ![首頁的 [說明及支援]](./media/support/homepage-help-support.png)

4. 選取 **[檔](../index.yml)** 以檢查問題和資源的完整解答。

5. 請選取 **[Marketplace 合作夥伴的社區論壇](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/bd-p/2222)** ，利用其他 Microsoft 發行者的知識回答您的問題。

6. 選取 [ **[其他協助](https://aka.ms/marketplacepublishersupport)** ] 以開啟 **新的支援要求** 票證。  

## <a name="how-to-open-a-support-ticket"></a>如何開啟支援票證

現在您已經準備好在 [說明 **及支援** ] 畫面上開啟支援票證。

![說明及支援](./media/support/help-and-support.png)

>[!Note]
>如果您已登入合作夥伴中心，將會獲得更佳的支援體驗。

**選項1：** 輸入關鍵字，例如： Marketplace、Azure 應用程式、SaaS 供應專案、帳戶管理、潛在客戶管理、部署問題、支出等。

**選項2：** 流覽主題-> 選取 **類別** = 商業 marketplace-> 選取適當的 **主題** ， **然後選取**[子主題]。

一旦找到您選擇的主題，請選取 [ **審核方案**]。

![後續步驟](./media/support/next-step.png)

下列選項將會變成可用：

- 若要選取不同的主題，請在 [選取的 **問題**] 下方選取不同的主題連結。
- 請檢查此問題的描述（如果有的話）。  這是在 **建議步驟** 中顯示的文字。
- 查看 **建議的步驟**（如果有的話）。
- 查看 **建議的檔**（如果有的話）。

![建議的解決方案](./media/support/recommended-solutions.png)

如果您在 **建議的解決方案** 中找不到您的答案，請選取 [ **提供問題詳細資料**]。 完成所有必要欄位，以加速解決流程，然後選取 [ **提交**]。

>[!Note]
>如果您尚未登入合作夥伴中心且主題需要驗證，您將會要求您登入，才能繼續進行。  若為公用主題，則不需要驗證。

## <a name="track-your-existing-support-requests"></a>追蹤您現有的支援要求

若要檢查所有開啟和關閉的票證，請移至左側導覽列上的 [ **商用 Marketplace** ]，然後選取 [ **支援**]。

## <a name="record-issue-details-with-a-har-file"></a>使用 HAR 檔案記錄問題詳細資料

若要協助支援工程師針對您的問題進行疑難排解，請考慮將 HTTP 封存格式 (HAR) 檔案附加至您的支援票證。 HAR 檔案是網頁瀏覽器中的網路要求記錄。

> [!WARNING]
> HAR 檔案可能會記錄有關您合作夥伴中心帳戶的機密資料。

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge 和 Google Chrome

若要使用 **Microsoft Edge** 或 **GOOGLE Chrome** 產生 HAR 檔案：

1. 移至您遇到問題的網頁。
2. 在視窗的右上角，選取省略號圖示，然後選取 [**其他工具**]  >  **開發人員工具**。 您可以按 F12 鍵作為快捷方式。
3. 在開發人員工具] 窗格中，選取 [ **網路** ] 索引標籤。
4. 選取 [ **停止記錄網路記錄** 檔並 **清除** ] 以移除現有的記錄檔。 記錄圖示將會變成灰色。

    ![如何在 Microsoft Edge 或 Google Chrome 中移除現有的記錄](media/support/chromium-stop-clear-session.png)

5. 選取 [ **記錄網路** 記錄檔] 以開始錄製。 當您開始錄製時，記錄圖示將會變成紅色。

    ![如何在 Microsoft Edge 或 Google Chrome 中開始錄製](media/support/chromium-start-session.png)

6. 重現您要進行疑難排解的問題。
7. 重現問題之後，請選取 [ **停止記錄網路記錄** 檔]。
8. 選取 [ **匯出 HAR**]，以向下箭號圖示標示，然後儲存檔案。

    ![如何在 Microsoft Edge 或 Google Chrome 中匯出 HAR 檔案](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

若要使用 **Mozilla Firefox** 產生 HAR 檔案：

1. 移至您遇到問題的網頁。
1. 在視窗的右上角，選取省略號圖示，然後選取 [ **Web Developer**  >  **切換工具**]。 您可以按 F12 鍵作為快捷方式。
1. 選取 [ **網路** ] 索引標籤，然後選取 [ **清除** ] 以移除現有的記錄。

    ![如何移除 Mozilla Firefox 中的現有記錄](media/support/firefox-clear-session.png)

1. 重現您要進行疑難排解的問題。
1. 重現問題之後，請選取 [ **har 匯出/匯入**  >  **全部儲存為 har**]。

    ![如何在 Mozilla Firefox 中匯出 HAR 檔案](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

使用 **Safari** 產生 HAR 檔案：

1. 啟用 safari 中的開發人員工具：選取 **safari**  >  **喜好** 設定。 移至 [ **Advanced** ] 索引標籤，然後選取 **功能表列中的 [顯示開發功能表**]。
1. 移至您遇到問題的網頁。
1. 選取 [開發]，然後選取 [顯示 Web 檢查程式]。
1. 選取 [ **網路** ] 索引標籤，然後選取 [ **清除網路專案** ] 以移除現有的記錄。

    ![如何在 Safari 中移除現有的記錄](media/support/safari-clear-session.png)

1. 重現您要進行疑難排解的問題。
1. 重現問題之後，請選取 [ **匯出** ] 並儲存檔案。

    ![如何在 Safari 中匯出 HAR 檔案](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](partner-center-portal/update-existing-offer.md)
