---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742673"
---
## <a name="examples"></a>範例

### <a name="filter-on-system-properties"></a>系統屬性篩選
若要參考篩選器中的系統屬性，請使用下列格式： `sys.<system-property-name>` 。 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>篩選訊息屬性
以下是在篩選準則中使用訊息屬性的範例。 您可以使用 `user.property-name` 或僅存取訊息屬性 `property-name` 。

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>篩選具有特殊字元的訊息屬性
如果訊息屬性名稱有特殊字元，請使用雙引號 (`"`) 來括住屬性名稱。 例如，如果屬性名稱是 `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` ，請在篩選準則中使用下列語法。 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>篩選具有數值的訊息屬性
下列範例會示範如何在篩選準則中使用具有數值的屬性。 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>以參數為基礎的篩選準則
以下是使用以參數為基礎的篩選器的一些範例。 在這些範例中， `DataTimeMp` 是類型的訊息屬性 `DateTime` ，而且 `@dtParam` 是以物件形式傳遞至篩選準則的參數 `DateTime` 。

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>使用 IN 和 NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

如需 c # 範例，請參閱 [GitHub 上的主題篩選範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)。


### <a name="set-rule-action-for-a-sql-filter"></a>設定 SQL 篩選準則的規則動作

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>使用 CorrelationID 的相互關聯篩選

```csharp
new CorrelationFilter("Contoso");
```

它會篩選 `CorrelationID` 設定為的訊息 `Contoso` 。 

### <a name="correlation-filter-using-system-and-user-properties"></a>使用系統和使用者屬性的相互關聯篩選

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

這相當於： `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

