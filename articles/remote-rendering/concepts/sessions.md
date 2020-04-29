---
title: 遠端轉譯工作階段
description: 描述遠端呈現會話是什麼
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681684"
---
# <a name="remote-rendering-sessions"></a>遠端轉譯工作階段

在 Azure 遠端轉譯（ARR）中，*會話*是重要的概念。 這篇文章說明會話到底是什麼。

## <a name="overview"></a>總覽

Azure 遠端呈現的運作方式是將複雜的轉譯工作卸載至雲端。 這些轉譯工作無法僅供任何伺服器完成，因為大部分的雲端伺服器都沒有 Gpu。 由於涉及的資料量和以互動畫面播放速率產生結果的硬性需求，因此，伺服器負責處理哪些使用者要求也無法立即傳遞至另一部電腦，可能會有更常見的 web 流量。

這表示當您使用 Azure 遠端轉譯時，必須以獨佔方式保留具有必要硬體功能的雲端伺服器，以處理您的轉譯要求。 *會話*指的是與此伺服器互動相關的所有專案。 它會從保留（*租用*）機器供您使用的初始要求開始，繼續進行載入和操作模型的所有命令，並在雲端伺服器上釋放租用結束。

## <a name="managing-sessions"></a>管理會話

有多種方式可以管理會話並與之互動。 建立、更新和關閉會話的與語言無關的方式，是透過[會話管理 REST API](../how-tos/session-rest-api.md)。 在 c # 和 c + + 中，這些作業會`AzureFrontend`透過`AzureSession`類別和公開。 對於 Unity 應用程式， `ARRServiceUnity`元件會提供進一步的公用程式函式。

*連接*到使用中的會話之後，像是[載入模型](models.md)和與場景互動的作業會透過`AzureSession`類別公開。

### <a name="managing-multiple-sessions-simultaneously"></a>同時管理多個會話

您無法從一個裝置完全*連接*到多個會話。 不過，您可以從單一應用程式建立、觀察和關閉多個會話。 只要應用程式不打算連線到會話，它就不需要在像是 HoloLens 2 的裝置上執行。 如果您想要透過中央機制來控制會話，這種執行的使用案例可能是。 例如，您可以建立一個 web 應用程式，讓多個平板電腦和 HoloLenses 都能登入。 然後，應用程式可以在平板電腦上顯示選項，例如要顯示的 CAD 型號。 如果使用者進行選取，則會將這項資訊傳達給所有 HoloLenses，以建立共用體驗。

## <a name="session-phases"></a>會話階段

每個會話都會經歷多個階段。

### <a name="session-startup"></a>會話啟動

當您要求 ARR[建立新的會話](../how-tos/session-rest-api.md#create-a-session)時，第一件事就是傳回會話[UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 此 UUID 可讓您查詢有關會話的資訊。 UUID 和會話的一些基本資訊會保存30天，因此即使在會話停止後，您仍可查詢該資訊。 此時，**會話狀態**將會回報為 [**啟動**]。

接下來，Azure 遠端轉譯會嘗試尋找可裝載您會話的伺服器。 此搜尋有兩個參數。 首先，它只會在您的[區域](../reference/regions.md)中保留伺服器。 這是因為跨區域的網路延遲可能太高，而無法保證適當的體驗。 第二個因素是您所指定的所需*大小*。 在每個區域中，可以滿足*標準**或高階*大小要求的伺服器數量有限。 因此，如果所要求大小的所有伺服器目前都在您的區域中使用，則會話建立將會失敗。 您[可以查詢](../how-tos/session-rest-api.md#get-sessions-properties)失敗的原因。

> [!IMPORTANT]
> 如果您要求*標準*VM 大小，而要求因為高需求而失敗，則不表示要求*高階伺服器也*會失敗。 因此，如果它是您的選項，您可以嘗試回到*Premium* VM。

當服務找到適合的伺服器時，必須將適當的虛擬機器（VM）複製到它，以將它轉換成 Azure 遠端轉譯主機。 這個程序需要幾分鐘的時間。 之後，VM 就會開機，且**會話狀態**會轉換成**Ready**。

此時，伺服器會獨佔等待您的輸入。 這也是您支付服務費用的那一點。

### <a name="connecting-to-a-session"></a>連接到會話

會話*準備就緒*之後，您就可以*連接*到它。 連線時，裝置可以傳送命令來載入和修改模型。 每個 ARR 主機一次只能提供一個用戶端裝置，因此當用戶端連線到會話時，它會對轉譯的內容具有獨佔控制。 這也表示，轉譯效能永遠不會因控制項外的原因而有所不同。

> [!IMPORTANT]
> 雖然只有一個用戶端可以*連接*到會話，但您可以在不連線的情況下查詢會話的相關基本資訊（例如其目前狀態）。

當裝置連線到會話時，其他裝置的嘗試連接將會失敗。 不過，一旦連接的裝置中斷連線（不論是主動或因為某種類型的失敗），會話將會接受另一個連接要求。 所有先前的狀態（已載入的模型和這類）都會被捨棄，讓下一個連接的裝置取得全新的平板電腦。 因此，您可以透過不同的裝置重複使用會話數次，而且可能會在大部分情況下隱藏使用者的會話啟動額外負荷。

> [!IMPORTANT]
> 遠端伺服器永遠不會改變用戶端資料的狀態。 所有資料變化（例如轉換更新和載入要求）都必須由用戶端應用程式執行。 所有動作都會立即更新用戶端狀態。

### <a name="session-end"></a>會話結束

當您要求新的會話時，您可以指定*最大租用時間*，通常是在一到八個小時的範圍內。 這是主機接受您輸入的持續時間。

會話結束的一般原因有兩個。 您可以手動要求停止會話，或租用時間上限已過期。 在這兩種情況下，主機的任何作用中連接都會立即關閉，而該伺服器上的服務會關閉。 接著，伺服器會被授與 Azure 集區，而且可能會因為其他目的而 requisitioned。 停止會話無法復原或取消。 查詢已停止會話的**會話狀態**時，將會傳回**已停止**或已**過期**，視其是否已手動關閉或已達到最大租用時間而定。

會話可能也會因為某些失敗而停止。

在所有情況下，一旦停止會話，就不會再向您收費。

> [!WARNING]
> 不論您是連接到會話，以及長時間，都不會影響帳單。 您支付服務的費用取決於會話的*持續時間*，這表示伺服器獨佔保留的時間，以及所要求的硬體功能（VM 大小）。 如果您啟動會話，請連接5分鐘，然後不要停止會話，讓它持續執行，直到其租用到期為止，您仍需支付完整的會話租用時間。 相反地，*最大租用時間*大部分是安全網。 如果您要求的會話有8小時的租用時間，當您之後手動停止會話時，就只會使用它5分鐘。

#### <a name="extend-a-sessions-lease-time"></a>延長會話的租用時間

您可以延長作用中會話的[租用時間](../how-tos/session-rest-api.md#update-a-session)，如果結果是您需要較長的時間。

## <a name="example-code"></a>程式碼範例

下列程式碼顯示啟動會話、等待*就緒*狀態、連接，然後再次中斷連線和關閉的簡單執行。

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

可以`AzureFrontend`從`AzureSession`程式碼維護、操作及查詢多個和實例。 但一次只能有一個裝置連接到`AzureSession` 。

虛擬機器的存留期不會系結至`AzureFrontend`實例或`AzureSession`實例。 `AzureSession.StopAsync`必須呼叫以停止會話。

持續性會話識別碼可以透過來查詢`AzureSession.SessionUUID()` ，並在本機快取。 使用此識別碼，應用程式可以呼叫`AzureFrontend.OpenSession`以系結至該會話。

當`AzureSession.IsConnected`為 true 時`AzureSession.Actions` ，會傳回的`RemoteManager`實例，其中包含[載入模型](models.md)、操作[實體](entities.md)，以及查詢所呈現之場景相關[資訊](../overview/features/spatial-queries.md)的函式。

## <a name="next-steps"></a>後續步驟

* [實體](entities.md)
* [模型](models.md)
