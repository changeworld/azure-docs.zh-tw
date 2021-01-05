---
title: IT 服務管理連接器-Azure 監視器中的安全匯出-Azure 設定
description: 本文說明如何設定 Azure，以便將您的 ITSM 產品/服務與 Azure 監視器中的安全匯出連線，以集中監視和管理 ITSM 工作專案。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 5eb58c48acc7974a4379cf1993a73228c99f5e6d
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857566"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>使用安全匯出將 Azure 設定為連接 ITSM 工具

本文提供如何設定 Azure 以使用「安全匯出」的相關資訊。
若要使用「安全匯出」，請遵循下列步驟：

1. [向 Azure AD 註冊應用程式.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [定義服務主體。](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [建立安全 Webhook 動作群組。](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. 設定您的夥伴環境。
    安全匯出支援連接與下列 ITSM 工具：
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>向 Azure Active Directory 註冊

請遵循下列步驟，向 Azure AD 註冊應用程式：

1. 遵循 [使用 Microsoft 身分識別平臺註冊應用程式](../../active-directory/develop/quickstart-register-app.md)中的步驟。
2. 在 Azure AD 中，選取 [ **公開應用程式**]。
3. 選取 [**應用程式識別碼 URI** 的 **設定**]。

   [![設定我的應用程式 U R I D 的選項螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. 選取 [儲存]。

## <a name="define-service-principal"></a>定義服務主體

動作群組服務將需要從您的 AAD 應用程式取得驗證權杖的許可權，才能立即使用服務進行驗證。 若要授與這些許可權，您必須為您租使用者中的動作群組服務建立服務主體。
您可以針對此目的使用此 [PowerShell 命令](./action-groups.md#secure-webhook-powershell-script) 。  (需要) 的租使用者系統管理員許可權。
選擇性的步驟是，您可以在建立的應用程式資訊清單中定義應用程式角色，這樣可讓您進一步限制存取，方法是只有具有該特定角色的特定應用程式可以傳送訊息。 此角色必須接著指派給動作群組服務主體。 \
您可以透過相同的 [PowerShell 命令](./action-groups.md#secure-webhook-powershell-script)來完成此步驟。

## <a name="create-a-secure-webhook-action-group"></a>建立安全 Webhook 動作群組

在您的應用程式註冊 Azure AD 之後，您可以使用動作群組中的安全 Webhook 動作，根據 Azure 警示在 ITSM 工具中建立工作專案。

動作群組提供模組化且可重複使用的方式來觸發 Azure 警示的動作。 您可以在 Azure 入口網站中使用具有計量警示、活動記錄警示和 Azure Log Analytics 警示的動作群組。
若要深入了解動作群組，請參閱[在 Azure 入口網站中建立和管理動作群組](./action-groups.md)。

若要將 webhook 新增至動作，請依照下列指示進行安全 Webhook：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，搜尋並選取 [監視器]。 [監視器] 頁面會將您的所有監視設定與資料合併在一個檢視中。
2. 選取 [**警示**  >  **管理動作**]。
3. 選取 [新增動作群組]，並填寫各欄位。
4. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。
5. 選取 [ **安全 Webhook**]。
6. 選取下列詳細資料：
   1. 選取您所註冊 Azure Active Directory 實例的物件識別碼。
   2. 針對 URI，貼上您從 [ITSM 工具環境](#configure-the-itsm-tool-environment)複製的 webhook URL。
   3. 將 **[啟用一般警示架構** ] 設定為 **[是]**。 

   下圖顯示範例安全 Webhook 動作的設定：

   ![顯示安全 Webhook 動作的螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>設定 ITSM 工具環境

此設定包含兩個步驟：

1. 取得安全匯出定義的 URI。
2. 以 ITSM 工具的流程為依據的定義。

## <a name="next-steps"></a>後續步驟

* [ServiceNow 安全匯出設定](./itsmc-secure-webhook-connections-servicenow.md)
* [BMC 安全匯出設定](./itsmc-secure-webhook-connections-bmc.md)
