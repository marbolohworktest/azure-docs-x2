---
author: t-leejiyoon
ms.service: azure-communication-services
ms.topic: include
ms.date: 07/28/2023
ms.author: t-leejiyoon
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-windows.md)]

The audio filter feature enable you to apply different audio preprocessing to outgoing audio. There are two types of audio filters: `OutgoingAudioFilters` and `LiveOutgoingAudioFilters`. Use `OutgoingAudioFilters` to change settings before the call starts and `LiveOutgoingAudioFilters` to change settings while a call is in progress.

You first need to import the Calling SDK:

```csharp
using Azure.Communication;
using Azure.Communication.Calling.WindowsClient;
```

## Before call starts

`OutgoingAudioFilters` can be applied when a call starts.

Begin by creating a `OutgoingAudioFilters` and passing it into OutgoingAudioOptions as shown in the following code:

```csharp
var outgoingAudioOptions = new OutgoingAudioOptions();
var filters = new OutgoingAudioFilters()
{
    AnalogAutomaticGainControlEnabled = true,
    DigitalAutomaticGainControlEnabled = true,
    MusicModeEnabled = true,
    AcousticEchoCancellationEnabled = true,
    NoiseSuppressionMode = NoiseSuppressionMode.High
};
outgoingAudioOptions.Filters = filters;
```

## During the call

You can apply `LiveOutgoingAudioFilters` after a call begins You can retrieve this object from the call object once the call begins. To change the setting in `LiveOutgoingAudioFilters`, set the members inside the class to a valid value and they're applied.

Only a subset of the filters available from `OutgoingAudioFilters` are available during an active call: music mode, echo cancellation, and noise suppression mode.

```csharp
LiveOutgoingAudioFilters filter = call.LiveOutgoingAudioFilters;
filter.MusicModeEnabled = true;
filter.AcousticEchoCancellationEnabled = true;
filter.NoiseSuppressionMode = NoiseSuppressionMode.Auto;
```
