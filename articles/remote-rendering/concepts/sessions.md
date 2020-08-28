---
title: 遠端轉譯工作階段
description: 介紹遠端轉譯工作階段
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 74fae6a8aa0c59043db0ab816e09b16affb63580
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021828"
---
# <a name="remote-rendering-sessions"></a>遠端轉譯工作階段

在 Azure 遠端轉譯 (ARR) 中，「工作階段」是很重要的概念。 本文會說明工作階段到底是什麼。

## <a name="overview"></a>概觀

Azure 遠端轉譯的運作方式，是將複雜的轉譯工作交由雲端處理。 這類轉譯工作並非任何伺服器都能完成，因為大多數雲端伺服器都沒有 GPU。 由於牽涉到的資料量龐大，在互動畫面播放速率下產生結果的需求又相當嚴苛，由哪台伺服器處理哪個使用者要求的責任也無法在作業途中交由另台機器，不像較為常見的 web 流量能如此處理。

這表示當您使用 Azure 遠端轉譯時，必須以獨佔方式保留具有必要硬體功能的雲端伺服器，以供處理轉譯要求。 「工作階段」指的是與此伺服器互動所涉及的所有內容。 從保留 (租用) 機器供您使用的初始要求開始，再到模型載入和操作的所有命令，到釋出雲端伺服器的租用為止。

## <a name="managing-sessions"></a>管理工作階段

有許多種方式可以管理工作階段並與之互動。 若要以不受程式設計語言影響的方式來建立、更新和關閉工作階段，可透過[工作階段管理 REST API](../how-tos/session-rest-api.md)。 在 C# 和 C++ 中，這類作業會透過類別 `AzureFrontend` 和 `AzureSession` 公開。 至於 Unity 應用程式，`ARRServiceUnity` 元件會提供進一步的公用程式函式。

「連線」至作用中的工作階段之後，[載入模型](models.md)和與場景互動的作業，就會透過 `AzureSession` 類別公開。

### <a name="managing-multiple-sessions-simultaneously"></a>同時管理多個工作階段

您無法從一台裝置完全「連線」到多個工作階段。 不過，您可以隨意從單一應用程式建立、觀察和關閉多個工作階段。 只要應用程式並不打算與工作階段連線，就不需要在 HoloLens 2 這類裝置上執行。 在想要透過中央機制控制工作階段的情況下，可能就會產生這類實作的使用案例。 例如，您可以組建一個 Web 應用程式，讓多台平板電腦和 HoloLenses 能夠登入。 然後，此應用程式可以在平板電腦上顯示選項，例如要顯示的 CAD 型號。 如果使用者進行選取，系統會將這項資訊傳達給所有 HoloLenses，以建立起共享體驗。

## <a name="session-phases"></a>工作階段的各個階段

每個工作階段都會經歷多個階段。

### <a name="session-startup"></a>工作階段啟動

當您要求 ARR [建立新的工作階段](../how-tos/session-rest-api.md#create-a-session)時，首先會傳回工作階段的 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 此 UUID 可用來查詢工作階段的相關資訊。 UUID 和工作階段的一些基本資訊會保存 30 天，因此即使在工作階段停止後，仍可查詢此一資訊。 此時，**工作階段狀態**會回報為**啟動**。

接下來，Azure 遠端轉譯會嘗試尋找可裝載工作階段的伺服器。 這種搜尋作業有兩種決定因素。 第一種是只會在您的[區域](../reference/regions.md)中保留伺服器。 這是因為跨區域的網路延遲可能過高，無法保證良好的體驗。 第二個因素在於您指定的所需「大小」。 在每個區域中，可滿足 [*標準*](../reference/vm-sizes.md) 或 [*Premium*](../reference/vm-sizes.md) 大小要求的伺服器數量會有所限制。 因此如果區域中符合所要求大小的伺服器全都在使用中，工作階段就會建立失敗。 您[可以查詢](../how-tos/session-rest-api.md#get-sessions-properties)失敗的原因。

> [!IMPORTANT]
> 如果您要求 *標準* 伺服器的大小，且要求因為高需求而失敗，則不表示要求 *高階伺服器也* 會失敗。 如果這是您可以選擇的選項，您可以嘗試回到 *高階伺服器大小* 。

當服務找到合適的伺服器時，就需要將適當的虛擬機器 (VM) 複製到伺服器上，將其轉換成 Azure 遠端轉譯主機。 這個程序需要幾分鐘的時間。 之後，VM 會開機，且**工作階段狀態**會轉換成**就緒**。

此時伺服器已在獨佔狀態，只會等待您的輸入。 這也是您開始支付服務費用的時間點。

### <a name="connecting-to-a-session"></a>連線到工作階段

工作階段狀態為「就緒」之後，您就可以「連線」到工作階段。 連線後，裝置可以傳送命令來載入和修改模型。 每個 ARR 主機一次只能服務一台用戶端裝置，因此當用戶端連線到工作階段時，會對轉譯內容具有獨佔控制權。 這也表示，轉譯效能永遠不會因控制範圍外的原因而有所差異。

> [!IMPORTANT]
> 雖然只有一個用戶端可以「連線」至工作階段，但查詢工作階段的相關基本資訊 (例如目前狀態) 則不需連線。

當有一台裝置連線到工作階段時，其他裝置的連線嘗試會失敗。 不過，一旦連線的裝置中斷連線 (不論是主動斷線或是某種類行的失敗)，工作階段就會接受另一個連線要求。 如此會捨棄所有先前的狀態 (載入的模型等)，讓下一個連線裝置取得原始狀態的工作階段。 因此，您可以透過不同的裝置重複使用工作階段數次，而且在大部分情況下，可能可以隱藏終端使用者的工作階段啟動額外負荷。

> [!IMPORTANT]
> 遠端伺服器永遠不會改變用戶端資料的狀態。 所有資料變動 (例如轉換更新和載入要求) 都必須由用戶端應用程式執行。 所有動作都會立即於用戶端狀態上更新。

### <a name="session-end"></a>工作階段結尾

當您要求新的工作階段時，可指定「租用時間上限」，範圍通常是在一到八個小時內。 這是主機會接受您輸入的持續時間。

工作階段結束的一般原因有兩個。 即是您手動要求停止工作階段，或超過租用時間上限。 在這兩種情況下，主機任何作用中的連線都會立即關閉，而該伺服器上的服務也會關閉。 接著，伺服器會回到 Azure 集區，且可能會因其他目的受到徵用。 您無法復原或取消工作階段的停止作業。 查詢已停止工作階段的**工作階段狀態**時，將會根據是遭到手動關閉還是已達到租用時間上限，傳回**已停止**或**已過期**。

工作階段可能也會因為某些失敗而停止。

不論任何情況，一旦停止工作階段，即不會再向您收費。

> [!WARNING]
> 不論是否連線到工作階段，也不論時間長短，都不會影響帳單。 您為服務支付的費用取決於 *會話持續時間*，這表示伺服器獨佔保留給您的時間，以及所要求的硬體功能 (配置的 [大小](../reference/vm-sizes.md)) 。 如果您啟動了工作階段並連線 5 分鐘，之後沒有停止工作階段，並持續執行直到租用到期為止，則需支付完整的工作階段租用時間費用。 相反地，「租用時間上限」基本算是一種防護機制。 如果您啟動工作階段後再手動停止，即使您申請的了 8 小時的工作階段租用時間，然後只使用了 5 分鐘，也是如此。

#### <a name="extend-a-sessions-lease-time"></a>延長工作階段的租用時間

如果您發現需要更多時間，也可以將作用中的工作階段[延長租用時間](../how-tos/session-rest-api.md#modify-and-query-session-properties)。

## <a name="example-code"></a>程式碼範例

下列程式碼是啟動工作階段的簡單實作範例，等待「就緒」狀態、連線，然後再中斷連線並再次關閉。

```cs
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
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
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

您可以透過程式碼維護、操作並查詢多個 `AzureFrontend` 和 `AzureSession` 執行個體。 但是每次只有一台裝置能連線至 `AzureSession`。

虛擬機器的存留期未繫結至 `AzureFrontend` 執行個體或 `AzureSession` 執行個體。 必須呼叫 `AzureSession.StopAsync` 以停止工作階段。

您可以透過 `AzureSession.SessionUUID()` 來查詢存留的工作階段識別碼，並在本機快取處理。 透過此識別碼，應用程式可以呼叫 `AzureFrontend.OpenSession` 以繫結至該工作階段。

當 `AzureSession.IsConnected` 為 True 時，`AzureSession.Actions` 會傳回 `RemoteManager`的執行個體，其中包含可[載入模型](models.md)、操作[實體](entities.md)和[查詢資訊](../overview/features/spatial-queries.md) (轉譯場景相關資訊) 的函式。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [模型](models.md)
