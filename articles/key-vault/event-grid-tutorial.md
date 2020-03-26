---
title: 使用 Azure 事件方格來接收和回應金鑰保存庫通知
description: 了解如何整合 Key Vault 與 Azure 事件方格。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 5eaf4cf702e56df932a61ab277dff6b34d97854d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78185024"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>使用 Azure 事件方格來接收和回應金鑰保存庫通知 (預覽)

Azure Key Vault 與 Azure 事件方格的整合 (目前處於預覽狀態) 可讓使用者在金鑰保存庫中儲存的祕密變更狀態時收到通知。 如需此功能的概觀，請參閱[使用事件方格監視 Key Vault](event-grid-overview.md)。

本指南說明如何透過事件方格接收 Key Vault 通知，以及如何透過 Azure 自動化回應狀態變更。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 訂用帳戶中的金鑰保存庫。 您可以遵循[使用 Azure CLI 從 Azure Key Vault 設定和擷取祕密](quick-create-cli.md)中的步驟，快速建立新的金鑰保存庫。

## <a name="concepts"></a>概念

事件方格是一項雲端事件服務。 藉由遵循本指南中的步驟，您將訂閱 Key Vault 的事件，並將事件路由傳送至 Azure 自動化。 當金鑰保存庫中的其中一個秘密即將過期時，事件方格會收到狀態變更通知並對端點進行 HTTP POST。 然後，Webhook 會觸發 PowerShell 指令碼的自動化執行。

![HTTP POST 流程圖](media/image1.png)

## <a name="create-an-automation-account"></a>建立自動化帳戶

透過 [Azure 入口網站](https://portal.azure.com)建立自動化帳戶。

1.  移至 portal.azure.com 並登入您的訂用帳戶。

1.  在搜尋方塊中，輸入**自動化帳戶**。

1.  在搜尋列下拉式清單的 [服務]  區段底下，選取 [自動化帳戶]  。

1.  選取 [新增]  。

    ![自動化帳戶窗格](media/image2.png)

1.  在 [新增自動化帳戶]  窗格中輸入必要資訊，然後選取 [建立]  。

## <a name="create-a-runbook"></a>建立 Runbook

自動化帳戶準備就緒之後，建立 Runbook。

![建立 Runbook UI](media/image3.png)

1.  選取您剛建立的自動化帳戶。

1.  在 [程序自動化]  下方，選取 [Runbook]  。

1.  選取 [建立 Runbook]  。

1.  為 Runbook 命名，然後選取 [PowerShell]  做為 Runbook 類型。

1.  選取您所建立的 Runbook，然後選取 [編輯]  按鈕。

1.  輸入下列程式碼 (用於測試目的)，然後選取 [發佈]  按鈕。 這個動作會傳回所接收 POST 要求的結果。

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![發佈 Runbook UI](media/image4.png)

## <a name="create-a-webhook"></a>建立 Webhook

建立 Webhook，以觸發新建立的 Runbook。

1.  從您剛發佈 Runbook 的 [資源]  區段中選取 [Webhook]  。

1.  選取 [新增 Webhook]  。

    ![新增 Webhook 按鈕](media/image5.png)

1.  選取 [建立新的 Webhook]  。

1. 為 Webhook 命名、設定到期日，然後複製 URL。

    > [!IMPORTANT] 
    > 建立 URL 之後，您就無法加以檢視。 務必將複本儲存在您可存取的安全位置，以供本指南的其餘部分使用。

1. 選取 [參數與回合設定]  ，然後選取 [確定]  。 請勿輸入任何參數。 這會啟用 [建立]  按鈕。

1. 選取 [確定]  ，然後選取 [建立]  。

    ![建立新的 Webhook UI](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>建立事件格線訂用帳戶

透過 [Azure 入口網站](https://portal.azure.com)建立事件方格訂用帳戶。

1.  移至您的金鑰保存庫，然後選取 [事件]  索引標籤。如果您看不到它，請確定您使用的是[入口網站的預覽版本](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)。

    ![Azure 入口網站中的事件索引標籤](media/image7.png)

1.  選取 [事件訂閱]  按鈕。

1.  建立訂用帳戶的描述性名稱。

1.  選擇 [事件方格結構描述]  。

1.  **主題資源**應該是您要監視其狀態變更的金鑰保存庫。

1.  針對 [篩選至事件類型]  ，讓所有選項保持選取 (**已選取 9 項**)。

1.  針對 [端點類型]  ，選取 [Webhook]  。

1.  選擇 [選取端點]  。 在新的內容窗格中，將[建立 Webhook](#create-a-webhook) 步驟中的 Webhook URL 貼到 [訂閱者端點]  欄位中。

1.  在內容窗格中選取 [確認選取項目]  。

1.  選取 [建立]  。

    ![建立事件訂閱](media/image8.png)

## <a name="test-and-verify"></a>測試和驗證

確認您的事件方格訂用帳戶已設定屬性。 這項測試假設您已在[建立事件方格訂用帳戶](#create-an-event-grid-subscription)中訂閱「已建立新版的秘密」通知，而且您有必要權限可在金鑰保存庫中建立新版的秘密。

![測試事件方格訂用帳戶的組態](media/image9.png)

![建立祕密窗格](media/image10.png)

1.  移至 Azure 入口網站上您的金鑰保存庫。

1.  建立新的祕密。 基於測試目的，請將到期日設定為後一天。

1.  在金鑰保存庫中的 [事件]  索引標籤上，選取您建立的事件方格訂用帳戶。

1.  在 [計量]  底下，檢查是否已擷取事件。 預期會有兩個事件：SecretNewVersion 和 SecretNearExpiry。 這些事件會驗證事件方格是否成功擷取到金鑰保存庫中祕密的狀態變更。

    ![計量窗格：檢查是否有已擷取的事件](media/image11.png)

1.  移至自動化帳戶。

1.  選取 [Runbook]  索引標籤，然後選取您建立的 Runbook。

1.  選取 [Webhook]  索引標籤，然後確認「上次觸發」時間戳記是在您建立新祕密時的 60 秒內。 這個結果可確認事件方格已使用金鑰保存庫中狀態變更的事件詳細資料對 Webhook 進行 POST，然後觸發 Webhook。

    ![Webhook 索引標籤，上次觸發時間戳記](media/image12.png)

1. 返回您的 Runbook，然後選取 [概觀]  索引標籤。

1. 查看 [最近的作業]  清單。 您應會看到已建立一項作業，且狀態為 [完成]。 這會確認 Webhook 觸發了 Runbook 來開始執行其指令碼。

    ![Webhook 最近的作業清單](media/image13.png)

1. 選取最近的作業，然後查看從事件方格傳送到 Webhook 的 POST 要求。 檢查 JSON，並確定您金鑰保存庫和事件類型的參數均正確無誤。 如果 JSON 物件中的「事件類型」參數符合金鑰保存庫中發生的事件 (在此範例中是 Microsoft.KeyVault.SecretNearExpiry)，則測試成功。

## <a name="troubleshooting"></a>疑難排解

### <a name="you-cant-create-an-event-subscription"></a>您無法建立事件訂閱。

在您的 Azure 訂用帳戶資源提供者中重新註冊事件方格和金鑰保存庫提供者。 請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="next-steps"></a>後續步驟

恭喜！ 如果您已正確遵循所有步驟，您現在已準備好以程式設計方式回應金鑰保存庫中所儲存祕密的狀態變更。

如果您已使用輪詢型系統來搜尋金鑰保存庫中祕密的狀態變更，現在可以開始使用此通知功能。 您也可以將 Runbook 中的測試指令碼取代為程式碼，在祕密即將到期時以程式設計方式進行更新。

深入了解：


- 概觀：[使用 Azure 事件方格監視 Key Vault (預覽)](event-grid-overview.md)
- 如何：[在金鑰保存庫祕密變更時收到電子郵件](event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件方格事件結構描述 (預覽)](../event-grid/event-schema-key-vault.md)
- [Azure Key Vault 概觀](key-vault-overview.md)
- [Azure 事件方格概觀](../event-grid/overview.md)
- [Azure 自動化概觀](../automation/index.yml)
