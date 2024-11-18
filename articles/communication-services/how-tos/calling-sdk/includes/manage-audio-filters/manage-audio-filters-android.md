---
author: zehangzheng
ms.service: azure-communication-services
ms.topic: include
ms.date: 10/6/2023
ms.author: zehangzheng
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

The audio filter feature enable you to apply different audio preprocessing to outgoing audio. There are two types of audio filters: `OutgoingAudioFilters` and `LiveOutgoingAudioFilters`. Use `OutgoingAudioFilters` to change settings before the call starts and `LiveOutgoingAudioFilters` to change settings while a call is in progress.

You first need to import the Calling SDK and the associated classes:

```csharp
import com.azure.android.communication.calling.OutgoingAudioOptions;
import com.azure.android.communication.calling.OutgoingAudioFilters;
import com.azure.android.communication.calling.LiveOutgoingAudioFilters;
```

## Before call starts

`OutgoingAudioFilters` can be applied when a call starts. 

Begin by creating a `OutgoingAudioFilters` and passing it into OutgoingAudioOptions as shown in the following code:

```java
OutgoingAudioOptions outgoingAudioOptions = new OutgoingAudioOptions();
OutgoingAudioFilters filters = new OutgoingAudioFilters();
filters.setNoiseSuppressionMode(NoiseSuppressionMode.HIGH);
filters.setAnalogAutomaticGainControlEnabled(true);
filters.setDigitalAutomaticGainControlEnabled(true);
filters.setMusicModeEnabled(true);
filters.setAcousticEchoCancellationEnabled(true); 
outgoingAudioOptions.setAudioFilters(filters);
```

## During the call

You can apply `LiveOutgoingAudioFilters` after a call begins You can retrieve this object from the call object once the call begins. To change the setting in `LiveOutgoingAudioFilters`, set the members inside the class to a valid value and they're applied.

Only a subset of the filters available from `OutgoingAudioFilters` are available during an active call: music mode, echo cancellation, and noise suppression mode.

```java
LiveOutgoingAudioFilters filters = call.getLiveOutgoingAudioFilters();
filters.setMusicModeEnabled(false);
filters.setAcousticEchoCancellationEnabled(false);
filters.setNoiseSuppressionMode(NoiseSuppressionMode.HIGH);
```
