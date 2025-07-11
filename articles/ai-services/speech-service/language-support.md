---
title: Language support - Speech service
titleSuffix: Azure AI services
description: The Speech service supports numerous languages for speech to text and text to speech conversion, along with speech translation. This article provides a comprehensive list of language support by service feature.
author: eric-urban
manager: nitinme
ms.service: azure-ai-speech
ms.topic: conceptual
ms.date: 6/5/2025
ms.author: eur
ms.custom: references_regions, build-2024
#Customer intent: As a developer, I want to learn about the languages supported by the Speech service.
---

# Language and voice support for the Speech service

The following tables summarize language support for [speech to text](speech-to-text.md), [text to speech](text-to-speech.md), [pronunciation assessment](how-to-pronunciation-assessment.md), [speech translation](speech-translation.md), [speaker recognition](speaker-recognition-overview.md), and more service features.

You can also get a list of locales and voices supported for each specific region or endpoint via:
- [Speech SDK](speech-sdk.md)
- [Speech to text REST API](rest-speech-to-text.md)
- [Speech to text REST API for short audio](rest-speech-to-text-short.md)
- [Text to speech REST API](rest-text-to-speech.md#get-a-list-of-voices)

## Supported languages

Language support varies by Speech service functionality. 

> [!NOTE]
> See [speech containers](speech-container-overview.md#available-speech-containers) and [embedded speech](embedded-speech.md#models-and-voices) documentation for their supported languages.

**Choose a Speech feature**

# [Speech to text](#tab/stt)

The table in this section summarizes the locales supported for [real-time speech to text](speech-to-text.md#real-time-speech-to-text), [fast transcription](speech-to-text.md#fast-transcription), and [batch transcription](speech-to-text.md#batch-transcription-api) transcription.

More remarks for speech to text locales are included in the [custom speech](#custom-speech) section of this article. 

> [!TIP]
> Try out the [Azure AI Speech Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-azureaispeech.azure-ai-speech-toolkit) to easily build and run samples on Visual Studio Code.

[!INCLUDE [Language support include](includes/language-support/stt.md)]

### Custom speech

To improve speech to text recognition accuracy, customization is available for some languages and base models. Depending on the locale, you can upload audio + human-labeled transcripts, plain text, structured text, and pronunciation data. By default, plain text customization is supported for all available base models. To learn more about customization, see [custom speech](./custom-speech-overview.md).

These locales support the [display text format feature](./how-to-custom-speech-display-text-format.md): da-DK, de-DE, en-AU, en-CA, en-GB, en-HK, en-IE, en-IN, en-NG, en-NZ, en-PH, en-SG, en-US, es-ES, es-MX, fi-FI, fr-CA, fr-FR, hi-IN, it-IT, ja-JP, ko-KR, nb-NO, nl-NL, pl-PL, pt-BR, pt-PT, sv-SE, tr-TR, zh-CN, zh-HK.


# [Text to speech](#tab/tts)

The table in this section summarizes the locales and voices supported for text to speech. For details, see the table footnotes.

More remarks for text to speech locales are included in the [voice styles and roles](#voice-styles-and-roles), [standard voices](#standard-voices), [professional voice](#professional-voice), and [personal voice](#personal-voice) sections in this article. 

> [!TIP]
> Check the [Voice Gallery](https://speech.microsoft.com/portal/voicegallery) and determine the right voice for your business needs.

> [!TIP]
> Try out the [Azure AI Speech Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-azureaispeech.azure-ai-speech-toolkit) to easily build and run samples on Visual Studio Code.

[!INCLUDE [Language support include](includes/language-support/tts.md)]

### Multilingual voices

Multilingual voices can support more languages. This expansion enhances your ability to express content in various languages, to overcome language barriers and foster a more inclusive global communication environment. 

Use this table to understand all supported speaking languages for each multilingual voice. If the voice doesn’t speak the language of the input text, the Speech service doesn't output synthesized audio. The table is sorted by the number of supported languages in descending order. The locale prefix indicates the primary locale. For example, for the voice `en-US-AndrewMultilingualNeural`, the locale prefix is `en-US`. The locale prefix is the first part of the voice name. 

[!INCLUDE [Language support include](includes/language-support/multilingual-voices.md)]

### Multi-talker voices

Multi-talker voices enable natural, dynamic conversations with multiple distinct speakers. This innovation enhances the realism of synthesized dialogues by preserving contextual flow, emotional consistency, and natural speech patterns.

Use this capability to generate engaging, podcast-style speech or conversational exchanges with seamless transitions between speakers. Unlike single-talker models, which synthesize each turn in isolation, multi-talker voices maintain coherence across dialogue, ensuring a more authentic and immersive listening experience.

[!INCLUDE [Language support include](includes/language-support/multi-talker.md)]

For more information about how to use multi-talker voices via Speech Synthesis Markup Language (SSML), see [Multi-talker voice](speech-synthesis-markup-voice.md#multi-talker-voice-example).

### Voice styles and roles

In some cases, you can adjust the speaking style to express different emotions like cheerfulness, empathy, and calm. All standard voices with speaking styles and multi-style custom voices support style degree adjustment. You can optimize the voice for different scenarios like customer service, newscast, and voice assistant. With roles, the same voice can act as a different age and gender.

To learn how you can configure and adjust voice styles and roles, see [Speech Synthesis Markup Language](speech-synthesis-markup-voice.md#use-speaking-styles-and-roles). 

Use the following table to determine supported styles and roles for each voice.

[!INCLUDE [Language support include](includes/language-support/voice-styles-and-roles.md)]


### Viseme

This table lists all the locales supported for [Viseme](speech-synthesis-markup-structure.md#viseme-element). For more information about Viseme, see [Get facial position with viseme](how-to-speech-synthesis-viseme.md) and [Viseme element](speech-synthesis-markup-structure.md#viseme-element). 

[!INCLUDE [Language support include](includes/language-support/viseme.md)]

### Standard voices

Each standard voice supports a specific language and dialect, identified by locale. You can try the demo and hear the voices in the [Voice Gallery](https://speech.microsoft.com/portal/voicegallery).

> [!IMPORTANT]
> Pricing varies for standard voice and custom voice. For more information, see the [text to speech pricing](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) page.

Each standard voice model is available at 24kHz and high-fidelity 48kHz. Other sample rates can be obtained through upsampling or downsampling when synthesizing.

### Professional voice

[Professional voice fine-tuning](./professional-voice-create-project.md) lets you create synthetic voices that are rich in speaking styles. You can create a unique brand voice in multiple languages and styles by using a small set of recording data. Multi-style custom voices support style degree adjustment. 

Select the right locale that matches your professional voice fine-tuning data. For example, if the recording data is spoken in English with a British accent, select `en-GB`.

With the cross-lingual feature, you can transfer your custom voice model to speak a second language. For example, with the `zh-CN` data, you can create a voice that speaks `en-AU` or any of the languages with cross-lingual support. For the cross-lingual feature, we categorize locales into two tiers: one includes source languages that support the cross-lingual feature, and the other tier comprises locales designated as target languages for cross-lingual transfer. Within the following table, distinguish locales that function as both cross-lingual sources and targets and locales eligible solely as the target locale for cross-lingual transfer. 

[!INCLUDE [Language support include](includes/language-support/tts-cnv.md)]


### Personal voice

[Personal voice](personal-voice-overview.md) is a feature that lets you create a voice that sounds like you or your users. The following table summarizes the locales supported for personal voice. 

[!INCLUDE [Language support include](includes/language-support/personal-voice.md)]


# [Pronunciation assessment](#tab/pronunciation-assessment)

The table in this section summarizes the 33 locales supported for pronunciation assessment, and each language is available on all [speech to text regions](regions.md#regions). Latest update extends support from English to 32 more languages and quality enhancements to existing features, including accuracy, fluency, and miscue assessment. You should specify the language that you're learning or practicing improving pronunciation. The default language is set as `en-US`. If you know your target learning language, [set the locale](how-to-pronunciation-assessment.md#get-pronunciation-assessment-results) accordingly. For example, if you're learning British English, you should specify the language as `en-GB`. If you're teaching a broader language, such as Spanish, and are uncertain about which locale to select, you can run various accent models (`es-ES`, `es-MX`) to determine the one that achieves the highest score to suit your specific scenario. If you're interested in languages not listed in the following table, fill out this [intake form](https://aka.ms/speechpa/intake) for further assistance.

[!INCLUDE [Language support include](includes/language-support/pronunciation-assessment.md)]

# [Speech translation](#tab/speech-translation)

> [!TIP]
> Try out the [Azure AI Speech Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-azureaispeech.azure-ai-speech-toolkit) to easily build and run samples on Visual Studio Code.

### Real-time speech translation

The table in this section summarizes the locales supported for Speech translation. Speech translation supports different languages for speech to speech and speech to text translation. The available target languages depend on whether the translation target is speech or text. 

#### Translate from language

To set the input speech recognition language, specify the full locale with a dash (`-`) separator. See the [speech to text language table](?tabs=stt#supported-languages). All languages are supported except `jv-ID` and `wuu-CN`. The default language is `en-US` if you don't specify a language.

#### Translate to text language

To set the translation target language, with few exceptions you only specify the language code that precedes the locale dash (`-`) separator. For example, use `es` for Spanish (Spain) instead of `es-ES`. See the speech translation target language table below. The default language is `en` if you don't specify a language.

[!INCLUDE [Language support include](includes/language-support/speech-translation.md)]

### Video translation

The table in this section summarizes the locales supported for the [video translation API](./video-translation-overview.md). Video translation supports different languages for standard (platform) voice and personal voice. The available source and target languages depend on whether the translation source is standard or personal voice.

[!INCLUDE [Language support include](includes/language-support/video-translation.md)]

# [Language identification](#tab/language-identification)

The table in this section summarizes the locales supported for [Language identification](language-identification.md).

> [!IMPORTANT]
> Language Identification compares speech at the language level, such as English and German. Don't include multiple locales of the same language in your candidate list.

[!INCLUDE [Language support include](includes/language-support/language-identification.md)]

# [Speaker recognition](#tab/speaker-recognition)

The table in this section summarizes the locales supported for Speaker recognition. Speaker recognition is mostly language agnostic. The universal model for text-independent speaker recognition combines various data sources from multiple languages. We tuned and evaluated the model on these languages and locales. For more information on speaker recognition, see the [overview](speaker-recognition-overview.md).

[!INCLUDE [Language support include](includes/language-support/speaker-recognition.md)]

# [Custom keyword](#tab/custom-keyword)

The table in this section summarizes the locales supported for custom keyword and keyword verification.

[!INCLUDE [Language support include](includes/language-support/custom-keyword.md)]

# [Intent Recognition](#tab/intent-recognizer-pattern-matcher)

The table in this section summarizes the locales supported for the Intent Recognizer Pattern Matcher.

[!INCLUDE [Language support include](includes/language-support/intent-recognizer-pattern-matcher.md)]

***

## Next steps

* [Region support](regions.md)
