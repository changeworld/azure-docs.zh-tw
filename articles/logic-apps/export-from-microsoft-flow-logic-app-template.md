---
title: 將流量從"電源自動"匯出到 Azure 邏輯應用
description: 通過匯出為 Azure 資源管理器範本，將流從"電源自動"遷移到 Azure 邏輯應用
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428872"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>從 Power Automate 匯出流程，並部署至 Azure Logic Apps

要擴展和擴展流的功能，可以將該流從["電源自動"](https://flow.microsoft.com)遷移到 Azure[邏輯應用](../logic-apps/logic-apps-overview.md)。 您可以將流匯出為邏輯應用的 Azure 資源管理器範本，將該邏輯應用範本部署到 Azure 資源組，然後在邏輯應用設計器中打開該邏輯應用。

> [!NOTE]
> 並非所有電源自動連接器在 Azure 邏輯應用中都可用。 可以導入 Azure 邏輯應用中具有[等效連接器](../connectors/apis-list.md)的流。 例如，"按鈕"觸發器、審批連接器和通知連接器特定于"電源自動"。
>
> 當前不支援從 Power Automate 匯出的基於 OpenAPI 的流作為邏輯應用範本進行部署。 

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要從電源自動功能輸出的流

## <a name="export-a-flow"></a>匯出流程

1. 登錄到[電源自動 ，](https://flow.microsoft.com)然後選擇 **"我的流**"。 查找並選擇您的流。 在工具列上，選擇橢圓 （**...**） 按鈕。 選擇**匯出** > **邏輯應用範本 （.json）。**

   ![匯出流程](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 將範本保存到所需的位置。

有關詳細資訊，請參閱["長到 Azure 邏輯應用](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)"。

## <a name="deploy-template-by-using-the-azure-portal"></a>使用 Azure 門戶部署範本

1. 使用 Azure 帳戶在[Azure 門戶](https://portal.azure.com)中簽名。

1. 在 Azure 主功能表上，選取 [建立資源]****。 在搜索框中，輸入"範本部署"。 選擇**範本部署（使用自訂範本部署），** 然後選擇 **"創建**"。

   ![選擇"範本部署"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. 在 **"自訂部署"** 下，在**編輯器中選擇"構建您自己的範本**"。

   ![選擇"在編輯器中構建您自己的範本"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. 在 **"編輯範本**"工具列中，選擇 **"載入檔**"。 查找並選擇從"電源自動"匯出的 JSON 範本，然後選擇 **"打開**"。

   ![選擇"載入檔"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. 編輯器在範本中顯示 JSON、參數和資源後，選擇 **"保存**"。
  
   ![儲存範本](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 現在為範本指定這些輸入參數：

   * 用於計費的 Azure 訂閱
   * Azure 資源群組
   * Azure 資源組的位置
   * 邏輯應用資源的名稱
   * 邏輯應用資源的位置（如果與 Azure 資源組不同）
   * 邏輯應用可以重用的任何以前創建的連接的名稱

      如果要創建第一個邏輯應用，則所有連接都作為新連接創建，因此可以接受預設名稱。 否則，您可以指定以前創建的連接的名稱，這些連接可以在多個邏輯應用中使用。

   為範本提供此資訊後，查看並同意 Azure 應用商店條款及條件，以便創建必要的 Azure 資源並相應地向 Azure 訂閱計費，然後選擇 **"購買**"。
  
   ![為範本指定輸入參數](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure 將範本作為邏輯應用部署到指定的資源組。 從 Power 自動遷移的所有邏輯應用都部署在禁用狀態。

1. 在啟動邏輯應用之前，請按照以下步驟授權任何新連接：

   1. 打開您創建的邏輯應用。 在邏輯應用的功能表上，選擇**邏輯應用設計器**。

      需要授權的每個連接都顯示一個警告圖示：

      ![警告圖示](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 對於需要授權連接的每個步驟，請展開該步驟，然後選擇"**添加新**"。

      ![增加新的連線](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 登錄到每個服務或提供必要的憑據以授權連接。

1. 儲存您的邏輯應用程式。 準備好啟動邏輯應用時，請在邏輯應用的功能表上選擇 **"概述****"，** 然後選擇"啟用 "。

   ![啟用邏輯應用](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 為避免運行重複工作流，請確保停用或刪除原始流。

## <a name="deploy-template-by-using-visual-studio"></a>使用視覺化工作室部署範本

如果已使用創建邏輯應用[的先決條件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)設置 Visual Studio，則可以將匯出的範本從視覺化工作室部署到 Azure 邏輯應用。

1. 在視覺化工作室中，打開從 Power 自動執行匯出的範本檔。

1. 在視覺化工作室中，創建 Azure 資源組專案，然後按照["快速入門：使用 Azure 邏輯應用 - 視覺化工作室"創建自動化任務、流程和工作流](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)的步驟選擇**邏輯應用**範本， 例如：

   ![建立 Azure 資源群組專案](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. 從解決方案資源管理器，打開**LogicApp.json**檔，如果檔尚未打開。

1. 從匯出的範本複製內容，並在**LogicApp.json**檔中覆蓋內容。

1. 在部署邏輯應用之前，請按照以下步驟授權任何新連接：

   1. 打開**LogicApp.json**快顯功能表，然後選擇 **"使用邏輯應用設計器打開**"。

      ![使用邏輯應用設計器打開範本](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. 如果出現提示，請選擇要用於部署邏輯應用的 Azure 訂閱和資源組。

      ![選擇 Azure 訂閱和資源組](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      邏輯應用在設計器中顯示後，任何需要授權的連接都顯示警告圖示：

      ![具有警告圖示的連接](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 對於需要授權連接的每個步驟，請展開該步驟，然後選擇"**添加新**"。

      ![增加新的連線](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 登錄到每個服務或提供必要的憑據以授權連接。

   1. 在部署邏輯應用之前保存解決方案。

1. 在解決方案資源管理器中，打開專案快捷方式功能表，然後選擇 **"部署** > **新建**"。 如果出現提示，登入您的 Azure 帳戶。

1. 提示後，確認 Azure 訂閱、Azure 資源組以及要用於部署的任何其他設置，例如用於傳遞範本參數值[的參數檔](../azure-resource-manager/templates/parameter-files.md)，然後選擇 **"部署**"。

   ![確認部署設置](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. 如果出現 **"編輯參數"** 框，請在 Azure 中提供邏輯應用資源的名稱，然後選擇"**保存**"。  

   ![編輯部署參數](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   開始部署時，您應用程式的部署狀態會顯示在 Visual Studio 的 [輸出]**** 視窗中。 如果狀態並未出現，請開啟 [顯示輸出來源]**** 清單，然後選取您的 Azure 資源群組。 例如：

   ![[輸出] 視窗](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   如果邏輯應用中的任何連接需要您輸入，則 PowerShell 視窗將在後臺打開，並提示輸入任何必要的密碼或金鑰。 當您輸入這些資訊之後，部署將會繼續。

   ![對連接進行身份驗證](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   部署完成後，邏輯應用將發佈，但不會在 Azure 門戶中啟動。

1. 準備好在 Azure 門戶中啟動邏輯應用後，在邏輯應用設計器中查找並打開邏輯應用。 在邏輯應用的功能表上，選擇 **"概述**"，然後選擇"**啟用**"。

1. 為避免運行重複工作流，請確保停用或刪除原始流。

有關這些部署步驟的詳細資訊，請參閱[快速入門：使用 Azure 邏輯應用創建自動化任務、流程和工作流 - 視覺化工作室](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 邏輯應用連接器](../connectors/apis-list.md)的更多資訊
* 瞭解有關[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)的更多資訊
