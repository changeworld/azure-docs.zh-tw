---
title: 遠端成成工作階段
description: 描述什麼是遠端呈現工作階段
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681684"
---
# <a name="remote-rendering-sessions"></a>遠端成成工作階段

在 Azure 遠端呈現 (ARR) 中,*會話*是一個關鍵概念。 本文介紹了會話的確切內容。

## <a name="overview"></a>概觀

Azure 遠端呈現的工作原理是將複雜的呈現任務卸載到雲中。 這些呈現任務不能僅僅由任何伺服器完成,因為大多數雲伺服器沒有 GPU。 由於涉及的數據量和以交互幀速率生成結果的硬性要求,因此使用者請求的伺服器處理的責任不能動態地移交給另一台計算機,因為對於更常見的 Web 流量,可能是可能的。

這意味著,在使用 Azure 遠端呈現時,必須專門保留具有必要硬體功能的雲端伺服器來處理呈現請求。 *會話*是指與此伺服器交互所涉及的所有內容。 它從初始請求保留(*租賃*)一台電腦供您使用,繼續載入和操作模型的所有命令,最後在雲端伺服器上釋放租約。

## <a name="managing-sessions"></a>管理工作階段

管理和與會話交互的方法有多種。 創建、更新和關閉工作階段的獨立於語言的方法是透過[工作階段管理 REST API。](../how-tos/session-rest-api.md) 在 C# 和 C++`AzureFrontend`中`AzureSession`,這些操作 通過 類和 公開。 對於 Unity`ARRServiceUnity`應用程式, 元件提供了其他實用程式函數。

*連接到*活動會話後,載入[模型](models.md)和與場景互動等操作將`AzureSession`通過 類公開。

### <a name="managing-multiple-sessions-simultaneously"></a>同時管理多個工作階段

無法從一台設備完全*連接到*多個會話。 但是,您可以創建、觀察和關閉從單個應用程式中根據需要創建的作業階段數。 只要應用程式不用於連接到作業階段,它也不需要在 HoloLens 2 等裝置上運行。 如果要通過中心機制控制會話,則此類實現的用例可能是。 例如,可以構建一個 Web 應用程式,其中多個平板電腦和 HoloLense 可以登錄。 然後,應用程式可以在平板電腦上顯示選項,例如要顯示的 CAD 模型。 如果用戶進行選擇,此資訊將傳達給所有 HoloLenses 以創建共享體驗。

## <a name="session-phases"></a>工作階段階段

每屆會議都經歷多個階段。

### <a name="session-startup"></a>工作階段啟動

當您要求 ARR[創建新工作階段時](../how-tos/session-rest-api.md#create-a-session),它做的第一件事是返回工作[階段 UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 此 UUID 允許您查詢有關會話的資訊。 UUID 和一些有關會話的基本資訊將保留 30 天,因此即使在會話停止後,您也可以查詢該資訊。 此時,**會話狀態**將報告為 **"開始**"。

接下來,Azure 遠程呈現嘗試查找可以承載會話的伺服器。 此搜索有兩個參數。 首先,它只會在您所在[地區](../reference/regions.md)保留伺服器。 這是因為跨區域的網路延遲可能太高,無法保證體面的體驗。 第二個因素是指定所需的*大小*。 在每個區域中,可以滿足*標準*或*高級*大小請求的伺服器數量有限。 因此,如果您所在區域當前正在使用請求大小的所有伺服器,則會話創建將失敗。 [可以查詢](../how-tos/session-rest-api.md#get-sessions-properties)失敗的原因。

> [!IMPORTANT]
> 如果請求*標準*VM 大小,並且請求由於高需求而失敗,這並不意味著請求*高級*伺服器也會失敗。 因此,如果它是一個選項,你可以嘗試回落到*高級*VM。

當服務找到合適的伺服器時,它必須將適當的虛擬機器(VM) 複製到它上,才能將其轉換為 Azure 遠端呈現主機。 這個程序需要幾分鐘的時間。 之後,VM被啟動,**會話狀態**將轉換為 **「就緒」。**

此時,伺服器將專門等待您的輸入。 這也是從中您獲得服務費用的點。

### <a name="connecting-to-a-session"></a>連線到工作階段

會話*準備就緒*後,即可*連接到*它。 連接時,設備可以發送命令以載入和修改模型。 每個 ARR 主機一次只提供一個用戶端設備,因此當用戶端連接到作業階段時,它可獨占對呈現的內容進行控制。 這也意味著渲染性能永遠不會因無法控制的原因而發生變化。

> [!IMPORTANT]
> 儘管只有一個用戶端可以*連接到*作業階段,但無需連接即可查詢有關會話的基本資訊,例如其當前狀態。

當設備連接到作業階段時,其他裝置連接的嘗試將失敗。 但是,一旦連接的設備自願斷開連接,或者由於某種故障,會話將接受另一個連接請求。 所有以前的狀態(載入的模型等)都會被丟棄,以便下一個連接設備獲得乾淨的石板。 因此,會話可以由不同的設備多次重複使用,在大多數情況下,可能會向最終使用者隱藏會話啟動開銷。

> [!IMPORTANT]
> 遠端伺服器永遠不會更改客戶端資料的狀態。 所有數據突變(如轉換更新和載入請求)必須由用戶端應用程式執行。 所有操作立即更新客戶端狀態。

### <a name="session-end"></a>工作階段結束

請求新會話時,指定*最大租約時間*,通常在 1 到 8 小時之間。 這是主機接受輸入的持續時間。

會話結束有兩個常規原因。 您手動請求停止會話或最大租約時間過期。 在這兩種情況下,與主機的任何活動連接都會馬上關閉,並且該服務將關閉該伺服器上。 然後,伺服器將返回 Azure 池,並可能出於其他目的被徵用。 無法撤消或取消停止作業階段。 查詢已停止會話上的**工作階段狀態**將返回 **「已停止」** 或 **「已過期**」,具體取決於是手動關閉還是由於已達到最大租約時間。

由於某些故障,會話也可能停止。

在所有情況下,一旦會話停止,您將不會被進一步計費。

> [!WARNING]
> 您是否連接到會話以及多長時間,不會影響計費。 您為服務支付的費用取決於*會話持續時間*,這意味著伺服器專門為您保留的時間以及請求的硬體功能(VM 大小)。 如果啟動會話,連接五分鐘,然後不停止會話,以便會話一直運行,直到其租約到期,您將為整個會話租賃時間計費。 相反,*最長租賃時間*主要是安全網。 是否請求租用時間為 8 小時的會話並不重要,如果之後手動停止會話,則僅使用五分鐘。

#### <a name="extend-a-sessions-lease-time"></a>延長工作階段的租約時間

如果事實證明需要活動會話的[租約時間](../how-tos/session-rest-api.md#update-a-session)較長,則可以延長它的時間。

## <a name="example-code"></a>程式碼範例

下面的代碼顯示了啟動工作階段、等待*就緒*狀態、連接,然後再次斷開連接和關閉的簡單實現。

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

可以從`AzureFrontend`代碼`AzureSession`維護、操作和查詢多個和實例。 但是,一次只能連接到一`AzureSession`個設備。

虛擬機的存留期不綁定`AzureFrontend`到 實`AzureSession`例或 實例。 `AzureSession.StopAsync`必須調用以停止會話。

持久會話 ID 可`AzureSession.SessionUUID()`透過本地查詢和快取。 使用此 ID,應用程式可以調`AzureFrontend.OpenSession`用 以綁定到該會話。

如果`AzureSession.IsConnected``AzureSession.Actions`為 true,`RemoteManager`則返回的實例,其中包含[載入模型](models.md)、操作[實體](entities.md)和查詢有關渲染場景[的](../overview/features/spatial-queries.md)函數。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [模型](models.md)
