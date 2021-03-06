---
title: "Azure Functions Notification Hub 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure 알림 허브 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions 알림 허브 출력 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure 알림 허브 바인딩을 구성하고 코딩하는 방법을 설명합니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

함수는 몇 줄의 코드로 구성된 Azure 알림 허브를 사용하여 푸시 알림을 보낼 수 있습니다. 그러나 Azure 알림 허브는 사용하려는 PNS(플랫폼 알림 서비스)에 대해 구성되어야 합니다. Azure 알림 허브를 구성하고 알림 수신을 위해 등록하는 클라이언트 응용 프로그램을 개발하는 데 대한 자세한 내용은 [알림 허브 시작](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 을 참조하고 맨 위에 있는 대상 클라이언트 플랫폼을 클릭합니다.

보내는 알림은 기본 알림 또는 템플릿 알림일 수 있습니다. 기본 알림은 출력 바인딩의 `platform` 속성에 구성된 특정 알림 플랫폼을 대상으로 합니다. 템플릿 알림은 여러 플랫폼을 대상으로 하는 데 사용할 수 있습니다.   

## <a name="notification-hub-output-binding-properties"></a>알림 허브 출력 바인딩 속성
function.json 파일은 다음 속성을 제공합니다.


|속성  |설명  |
|---------|---------|
|**name** | 알림 허브 메시지에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**type** | `notificationHub`로 설정해야 합니다. |
|**tagExpression** | 태그 식을 사용하면 알림을 태그 식과 일치하는 알림을 수신하도록 등록된 일련의 장치에 배달하도록 지정할 수 있습니다.  자세한 내용은 [라우팅 및 태그 식](../notification-hubs/notification-hubs-tags-segment-push-message.md)을 참조하세요. |
|**hubName** | Azure Portal에서 알림 허브 리소스의 이름입니다. |
|**연결** | 이 연결 문자열은 알림 허브에 대한 *DefaultFullSharedAccessSignature* 값으로 설정된 **Application Setting** 연결 문자열이어야 합니다. |
|**direction** | `out`로 설정해야 합니다. | 
|**platform** | 플랫폼 속성은 알림의 대상으로 지정된 알림 플랫폼을 나타냅니다. 기본적으로 출력 바인딩에서 platform 속성을 생략하면 템플릿 알림을 사용하여 Azure Notification Hub에 구성된 플랫폼을 대상으로 지정할 수 있습니다. 일반적으로 Azure 알림 허브 알림에서 템플릿을 사용하여 플랫폼 간 알림을 보내는 방법에 대한 자세한 내용은 [템플릿](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)을 참조하세요. _platform_을 설정한 경우 다음 값 중 하나여야 합니다. <ul><li><code>apns</code>&mdash;Apple Push Notification Service. APNS에 대한 알림 허브를 구성하고 클라이언트 앱에서 알림을 받는 방법에 대한 자세한 내용은 [Azure Notification Hubs를 사용하여 iOS에 푸시 알림 보내기](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)를 참조하세요.</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). ADM에 대한 알림 허브를 구성하고 Kindle 앱에서 알림을 받는 방법에 대한 자세한 내용은 [Kindle 앱에 대한 알림 허브 시작](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)을 참조하세요.</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). 새 버전의 GCM인 Firebase Cloud Messaging도 지원됩니다. 자세한 내용은 [Azure Notification Hubs를 사용하여 Android에 푸시 알림 보내기](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)를 참조하세요.</li><li><code>wns</code>&mdash;Windows 플랫폼을 대상으로 하는 [Windows 푸시 알림 서비스](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) Windows Phone 8.1 이상도 WNS에서 지원됩니다. 자세한 내용은 [Windows 유니버설 플랫폼 앱용 Notification Hubs 시작](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)을 참조하세요.</li><li><code>mpns</code>&mdash;[Microsoft 푸시 알림 서비스](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). 이 플랫폼은 Windows Phone 8 및 이전 Windows Phone 플랫폼을 지원합니다. 자세한 내용은 [Windows Phone에서 Azure Notification Hubs를 사용하여 푸시 알림 보내기](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)를 참조하세요.</li></ul> |

예제 function.json:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>알림 허브 연결 문자열 설정
알림 허브 출력 바인딩을 사용하려면 허브에 대한 연결 문자열을 구성해야 합니다. 기존 알림 허브를 선택하거나 함수의 *통합* 탭에서 새 알림 허브를 만들 수 있습니다. 연결 문자열을 수동으로 구성할 수도 있습니다. 

연결 문자열을 기존 알림 허브로 구성하려면

1. [Azure Portal](https://portal.azure.com)에서 알림 허브로 이동한 후 **액세스 정책**을 선택하고 **DefaultFullSharedAccessSignature** 정책 옆에 있는 복사 단추를 선택합니다. 그러면 *DefaultFullSharedAccessSignature* 정책에 대한 연결 문자열이 알림 허브로 복사됩니다. 이 연결 문자열에서는 알림 메시지를 보내는 함수 액세스 권한을 제공합니다. 
    ![알림 허브 연결 문자열 복사](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Azure Portal에서 함수 앱으로 이동한 후 **응용 프로그램 설정**을 선택하고 `MyHubConnectionString`과 같은 키를 추가한 다음, 알림 허브에 대해 복사한 *DefaultFullSharedAccessSignature*를 값으로 붙여 넣고 **저장**을 클릭합니다.

이제 출력 바인딩에서 알림 허브 연결을 정의하는 이러한 명명된 응용 프로그램 설정을 사용할 수 있습니다.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>C# 큐 트리거를 사용한 APNS 기본 알림
이 예제에서는 [Microsoft Azure Notification Hubs 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 형식을 사용하여 기본 APNS 알림을 보내는 방법을 보여 줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>C# 큐 트리거를 사용한 GCM 기본 알림
이 예제에서는 [Microsoft Azure Notification Hubs 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 형식을 사용하여 기본 GCM 알림을 보내는 방법을 보여 줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>C# 큐 트리거를 사용한 WNS 기본 알림
이 예제에서는 [Microsoft Azure Notification Hubs 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 형식을 사용하여 기본 WNS 알림 메시지를 보내는 방법을 보여 줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Node.js 타이머 트리거에 대한 템플릿 예제
이 예제에서는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>F# 타이머 트리거에 대한 템플릿 예제
이 예제에서는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>out 매개 변수를 사용하는 템플릿 예제
이 예제에서는 템플릿에 `message` 자리 표시자가 포함된 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>비동기 함수를 사용하는 템플릿 예제
비동기 코드를 사용하는 경우 out 매개 변수가 허용되지 않습니다. 이 경우 `IAsyncCollector`를 사용하여 템플릿 알림을 반환합니다. 다음 코드는 위 코드의 비동기 예제입니다. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>JSON을 사용하는 템플릿 예제
이 예제에서는 유효한 JSON 문자열을 사용하여 템플릿에 `message` 자리 표시자가 포함된 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>알림 허브 라이브러리 형식을 사용하는 템플릿 예제
이 예제에서는 [Microsoft Azure Notification Hubs 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 형식을 사용하는 방법을 보여 줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

