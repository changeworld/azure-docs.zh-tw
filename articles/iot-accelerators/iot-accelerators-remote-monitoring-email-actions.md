---
title: 遠端監視內的電子郵件動作 - Azure | Microsoft Docs
description: 本操作指南示範如何將電子郵件動作新增至新的或現有規則。
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 2776b18dc3faded103b49e8ff58a8a0a6dccf144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074459"
---
# <a name="add-an-email-action"></a>新增電子郵件動作

電子郵件的動作有助於確保您不會錯過任何警示。 當您建立新的規則時，您可以將電子郵件動作新增至現有規則。

若要完成本操作指南中的步驟，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案加速器執行個體。

若要建立或修改規則，您必須是[**系統管理員**，或具有正確的權限](iot-accelerators-remote-monitoring-rbac.md)。

## <a name="edit-an-existing-rule"></a>編輯現有的規則

遵循下列步驟，將電子郵件動作新增至現有規則：

1. 瀏覽至您的遠端監視解決方案。

1. 從 [儀表板]****，瀏覽至 [規則]**** 頁面：

    ![規則頁面](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. 按一下要修改的現有規則旁的核取方塊，然後按一下頂端的 [編輯]****。 可編輯的 [規則]**** 面板隨即出現。

1. 在 [動作]**** 區段中，將 [已啟用電子郵件]**** 切換為 [開啟]****。

1. 第一次在解決方案加速器中啟用電子郵件動作時，您必須[登入 Outlook](#outlook)。

1. 在收件者方塊中輸入電子郵件地址，然後針對要新增的每個電子郵件地址按 **Enter** 鍵：

    ![地址項目](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. 輸入電子郵件的主旨。

1. 以純文字形式輸入給電子郵件收件者的任何其他備註。 如果您[編輯電子郵件範本](#htmledit)，則可使用 HTML 格式。

1. 確定 [規則狀態]**** 已設為 [已啟用]****。

1. 按一下 [套用]。

## <a name="create-a-new-rule"></a>建立新的規則

建立新規則時，請遵循下列步驟來新增電子郵件動作：

1. 瀏覽至您的遠端監視解決方案。

1. 從 [儀表板]****，瀏覽至 [規則]**** 頁面：

    ![規則頁面](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. 請遵循[建立規則](iot-accelerators-remote-monitoring-automate.md#create-a-rule)一節中的步驟。 請遵循[建立進階規則](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule)一節中的步驟，直到您設定**嚴重性層級**的那一點。 還不要按 [套用]****。

1. 在 [動作]**** 區段中，將 [已啟用電子郵件]**** 切換為 [開啟]****。

1. 第一次在解決方案加速器中啟用電子郵件動作時，您必須[登入 Outlook](#outlook)。

1. 在收件者方塊中輸入電子郵件地址，然後針對要新增的每個電子郵件地址按 **Enter** 鍵：

    ![地址項目](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. 輸入電子郵件的主旨。

1. 以純文字形式輸入給電子郵件收件者的任何其他備註。 如果您[編輯電子郵件範本](#htmledit)，則可使用 HTML 格式。

1. 確定 [規則狀態]**** 已設為 [已啟用]****。

1. 按一下 [套用]。

現在已啟用您具有電子郵件動作的規則。 每次觸發此動作時，就會將新的電子郵件傳送給收件者。

## <a name="sign-in-to-outlook"></a>登入 Outlook <a name="outlook"></a>

第一次在解決方案加速器中啟用電子郵件動作時，您必須登入 Outlook。 此動作會設定送出電子郵件通知的電子郵件帳戶。

> [!NOTE]
> 您應該建立只是用於解決方案加速器通知的特定 Outlook 帳戶，並且在您啟用第一個電子郵件動作時使用該帳戶。

### <a name="contributor-role-outlook-setup"></a>參與者角色 Outlook 設定

如果某個具有訂用帳戶中**參與者**角色的人員部署了解決方案加速器，則應用程式沒有足夠的權限來透過 Web UI 設定及驗證電子郵件動作。

開始之前，建立 Outlook 帳戶，用來傳送來自解決方案加速器的電子郵件通知。

下列步驟顯示如何手動設定及驗證電子郵件動作：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 瀏覽至您解決方案加速器的資源群組。

1. 按一下 [office365-connector]****：

    ![API 連線](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. 按一下橫幅以開始授權程序：

    ![授權](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. 按一下 [授權]****。 系統會提示您登入。 您用來登入的帳戶應該是應用程式用來傳送電子郵件通知的電子郵件地址：

    ![授權按鈕](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. 按一下底部的 [儲存]****。 如果橫幅消失，您的授權將會成功。

1. 若要變更傳來通知的來源電子郵件地址，請按一下 [編輯 API 連線]****。

    ![變更電子郵件](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>擁有者角色 Outlook 設定

如果某個具有訂用帳戶中**擁有者**角色的人員部署了解決方案加速器，則應用程式可以透過 Web UI 驗證電子郵件動作是否設定正確。

開始之前，建立 Outlook 帳戶，用來傳送來自解決方案加速器的電子郵件通知。

下列步驟可協助您登入並設定電子郵件動作：

1. 按一下即可登入 Outlook。 您會前往 Azure 入口網站：

   ![登入 Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. 按一下 [授權]****。 系統會提示您登入。 您用來登入的帳戶應該是應用程式用來傳送電子郵件通知的電子郵件地址：

1. 按一下 [儲存]。 移至您的解決方案加速器並重新整理頁面。

1. 如果您已成功設定電子郵件通知，您會看到此訊息：

   ![成功的 Outlook 登入](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>自訂電子郵件 HTML <a name="htmledit"></a>

現成的遠端監視解決方案加速器會提供動作電子郵件的基本 HTML 範本。 電子郵件範本會使用來自電子郵件動作設定的值。 以下是範例電子郵件：

![電子郵件範例](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

下列步驟說明如何編輯 HTML 電子郵件範本。 例如，您可以包含更多資訊或新增自訂映像：

1. 複製 Java 或 .NET 遠端監視 GitHub 存放庫︰

1. 瀏覽到電子郵件範本位置：
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. 您可以在此範本中新增或移除任何參數，以自訂訊息。 您也可以視需要新增、移除或取代呼叫：

    例如，在 .NET 程式碼中：`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    例如，在 Java 程式碼中：`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. 範本中的參數採用 `${...}` 形式。 若要刪除參數，請刪除必要的程式碼行。 若要新增參數，請新增包含要插入值的一行。

1. 若要新增映像或自訂文字，請直接更新 EmailTemplate.HTML 檔案。

## <a name="throttling"></a>節流

遠端監視解決方案加速器會使用 Outlook 來傳送電子郵件通知。 Outlook 會將傳送的電子郵件數目限制為[每 1 分鐘 30 封電子郵件](/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits)。 接收電子郵件的電子郵件用戶端也可能會節流處理每分鐘收到的電子郵件數目。 請向您的特定電子郵件用戶端洽詢相關限制。 當您設定規則的電子郵件通知時，此規則應會計算一段時間 (至少一分鐘) 的平均值，而不會使用即時值：

![平均計算](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>後續步驟

本指南說明了如何在遠端監視解決方案中將電子郵件動作新增至新的或現有規則。 本指南也說明了如何編輯可定義訊息格式的 HTML。

建議的下一個步驟是了解[如何使用警示及修正裝置問題](iot-accelerators-remote-monitoring-maintain.md)。