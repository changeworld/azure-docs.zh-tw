---
title: 取得合作夥伴中心中的商業 marketplace 方案支援
description: 您可以在合作夥伴中心中瞭解商用 marketplace 方案的支援選項，包括如何提出支援要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: 6075027124352746a3adbb8f6937d3787eb22b9d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602573"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>合作夥伴中心中的商業 marketplace 方案支援

Microsoft 提供各式各樣的產品和服務支援。 尋找正確的支援小組很重要，可確保適當且及時的回應。 請考慮下列案例，這些案例應有助於將您的查詢傳送給適當的團隊：

- 如果您是發行者，並有來自客戶的問題，請要求您的客戶使用 [Azure 入口網站](https://portal.azure.com/)中的支援連結來要求支援。
- 如果您是發行者，而且偵測到在 Azure 上執行的應用程式有安全性問題，請參閱 [如何記錄安全性事件支援票證](/azure/security/fundamentals/event-support-ticket)。 發行者一旦發現可疑的安全性事件，必須立即報告，包括其 Azure Marketplace 軟體和服務供應項目的安全性事件和弱點。
- 如果您是發行者，並且有與您的應用程式或服務相關的問題，請參閱下列支援選項。

## <a name="get-help-or-open-a-support-ticket"></a>取得協助或開啟支援票證

1. 使用您的工作帳戶登入。 如果您尚未這麼做，您將需要 [建立合作夥伴中心帳戶](partner-center-portal/create-account.md)。

1. 在頁面右上角的功能表中，選取 [ **支援** ] 圖示。 [說明 **及支援** ] 窗格會出現在頁面的右側。

1. 若要取得商用 marketplace 的協助，請選取 [ **商用 marketplace**]。

   ![支援下拉式功能表](./media/support/commercial-marketplace-support-pane.png)

1. 在 [ **問題摘要** ] 方塊中，輸入問題的簡短描述。

1. 在 [ **問題類型** ] 方塊中，執行下列其中一項：

    - **選項 1**：輸入關鍵字，例如： Marketplace、Azure 應用程式、SaaS 供應專案、帳戶管理、潛在客戶管理、部署問題、付款或共同銷售供應專案的遷移。 然後從出現的建議清單中選取問題類型。

    - **選項 2**：選取 [**類別**] 清單中的 **[流覽主題]** ，然後選取 [**商業 Marketplace**]。 然後選取適當的 **主題** 和 **子主題。**

1. 找到您所選擇的主題之後，請選取 [ **審核方案**]。

    ![後續步驟](./media/support/next-step.png)

下列選項如下所示：

- 若要選取不同的主題，請按一下 [ **選取不同的問題**]。
- 若要協助解決此問題，請參閱建議的步驟和檔（如果有的話）。

    ![建議的解決方案](./media/support/recommended-solutions.png)

如果您在自助的 [說明] 中找不到答案，請選取 [ **提供問題詳細資料**]。 完成所有必要欄位，以加速解決流程，然後選取 [ **提交**]。

>[!Note]
>如果您尚未登入合作夥伴中心，您可能需要先登入，才能建立票證。

## <a name="track-your-existing-support-requests"></a>追蹤您現有的支援要求

若要檢查開啟和關閉的票證，請在左側導覽功能表中選取 [**商業 Marketplace**  >  **支援**]。

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
