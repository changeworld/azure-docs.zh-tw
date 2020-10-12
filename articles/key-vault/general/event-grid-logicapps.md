---
title: 在 Key Vault 祕密狀態變更時傳送電子郵件
description: 使用 Logic Apps 來回應 Key Vault 祕密變更的指南
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 2781ef69ce85e82dab45a9f890ef5e6862949d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087978"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>使用 Logic Apps 接收有關金鑰保存庫祕密狀態變更的電子郵件

在本指南中，您將了解如何使用 [Azure Logic Apps](../../logic-apps/index.yml) 來回應透過 [Azure 事件方格](../../event-grid/index.yml)所收到的 Azure Key Vault 事件。 您會在結束前，將 Azure 邏輯應用程式設定為在每次於 Azure Key Vault 中建立祕密時傳送通知電子郵件。

如需 Azure Key Vault/Azure 事件方格整合的概觀，請參閱[使用 Azure 事件方格監視 Key Vault](event-grid-overview.md)。

## <a name="prerequisites"></a>Prerequisites

- Azure Logic Apps 支援的任何電子郵件提供者 (例如 Office 365 Outlook) 所提供的電子郵件帳戶。 這個電子郵件帳戶用來傳送事件通知。 如需支援的完整邏輯應用程式連接器清單，請參閱[連接器概觀](/connectors)
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 訂用帳戶中的金鑰保存庫。 您可以遵循[使用 Azure CLI 從 Azure Key Vault 設定和擷取祕密](../secrets/quick-create-cli.md)中的步驟，快速建立新的金鑰保存庫。
- 已將事件方格註冊為資源提供者，請參閱[資源提供者註冊](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

## <a name="create-a-logic-app-via-event-grid"></a>透過事件方格建立邏輯應用程式

首先，使用事件方格處理常式建立邏輯應用程式，並訂閱 Azure Key Vault "SecretNewVersionCreated" 事件。

若要建立 Azure 事件方格訂用帳戶，請遵循下列步驟：

1. 在 Azure 入口網站中，移至您的金鑰保存庫，選取 [事件] > [開始使用]**** 然後按一下 [Logic Apps]****。

    
    ![Key Vault - 事件頁面](../media/eventgrid-logicapps-kvsubs.png)

1. 在 [Logic Apps 設計工具]**** 上驗證連線，然後按一下 [繼續]****。 
 
    ![邏輯應用程式設計工具 - 連線](../media/eventgrid-logicappdesigner1.png)

1. 在 [發生資源事件時]**** 畫面上，執行下列操作：
    - 保留 [訂用帳戶]**** 和 [資源名稱]**** 的預設值。
    - 針對 [資源類型]**** 選取 [Microsoft.KeyVault.vaults]****。
    - 針對 [事件類型項目 - 1]**** 選取 [Microsoft.KeyVault.SecretNewVersionCreated]****。

    ![邏輯應用程式設計工具 - 事件處理常式](../media/eventgrid-logicappdesigner2.png)

1. 選取 [+ 新增步驟]****。這樣會開啟 [選擇動作] 的視窗。
1. 搜尋**電子郵件**。 根據您的電子郵件提供者，尋找並選取相符的連接器。 本教學課程使用 **Office 365 Outlook**。 其他電子郵件提供者的步驟很類似。
1. 選取 [傳送電子郵件 \(V2\)]**** 動作。

   ![邏輯應用程式設計工具 - 傳送電子郵件](../media/eventgrid-logicappdesigner3.png)

1. 建置您的電子郵件範本：
    - **收件人：** 輸入要收到通知電子郵件的電子郵件地址。 在此教學課程中，請使用您可以存取的電子郵件帳戶來進行測試。
    - **主旨**和**本文**：撰寫電子郵件內容。 從選取器工具選取 [JSON 屬性]，以便根據事件資料納入動態內容。 您可以使用 `@{triggerBody()?['Data']}` 來擷取事件的資料。

    您的電子郵件範本可能類似此範例。

    ![邏輯應用程式設計工具 - 電子郵件內文](../media/eventgrid-logicappdesigner4.png)

8. 按一下 [另存新檔]****。
9. 為新的邏輯應用程式輸入**名稱**，然後按一下 [建立]****。
    
    ![邏輯應用程式設計工具 - 建立](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>測試和驗證

1.  移至 Azure 入口網站上的金鑰保存庫，然後選取 [事件] > [事件訂閱]****。  確認已建立新的訂用帳戶
    
    ![邏輯應用程式設計工具 - 測試和驗證](../media/eventgrid-logicapps-kvnewsubs.png)

1.  移至您的金鑰保存庫，選取 [祕密]****，然後選取 [+ 產生/匯入]****。 建立新的祕密以供測試之用，為金鑰命名，並將其餘的參數保留為預設設定。

    ![Key Vault - 建立祕密](../media/eventgrid-logicapps-kv-create-secret.png)

1. 在 [建立祕密]**** 畫面上提供任何名稱、任何值，然後選取 [建立]****。

建立祕密時，會在設定的電子郵件地址收到電子郵件。

## <a name="next-steps"></a>後續步驟

- 概觀：[使用 Azure 事件方格監視 Key Vault](event-grid-overview.md)
- 如何：[將金鑰保存庫通知路由傳送至 Azure 自動化](event-grid-tutorial.md)。
- [Azure Key Vault 的 Azure 事件方格事件結構描述](../../event-grid/event-schema-key-vault.md)
- 深入了解 [Azure Event Grid](../../event-grid/index.yml)。
- 深入了解 [Azure App Service 的 Logic Apps 功能](../../logic-apps/index.yml)。
