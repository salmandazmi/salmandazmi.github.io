---
layout: post
title:  "Strategic Responses of Indonesian Companies to the COVID-19 Pandemic"
author: salmandazmi
categories: [ Portofolio ]
tags: [ Tesis ]
image: assets/images/complex-aerial-view-city.jpg
---

The COVID-19 pandemic creates many challenges for the sustainability of various companies, including Indonesian companies. Every company is affected in one way or another by this outbreak. Based on the content analysis of 225 selected news items from Indonesian online business news portal published from May to November 2020, this study analyzes various strategic responses of Indonesian companies to the COVID-19 pandemic. It also identifies the patterns of shifting between strategic responses that have occurred. These general strategic responses to the crisis include retrenchment, persevering, innovating, and exit (Wenzel et al., 2020). This study uses an automatic text summarization technique to assist the data analysis process.

_**Authors:** Salman Azmi, Nofie Iman_

<!-- # Introduction
# Research Question
# Data -->
## Scraping

In the data collection process, links from all online news items published on [SWA’s specific page for the COVID-19 rubric](https://swa.co.id/covid19) were collected. Collected links are filtered to prevent duplication. Then, all texts of online news items are downloaded according to the links collected using the Python programming language [Newspaper3k](https://github.com/codelucas/newspaper) library. This library was chosen because of its ease of use.

## Summarization

At this stage, each collected news item is summarized using an automatic text summarization technique. It is conducted to get important information from the news in a shorter form to assist the analysis process (Kurniawan & Louvan, 2019). There are two sorts of automatic text summarization; extractive and abstractive. In this study, the researcher uses an abstractive summary technique to produce a summary containing paraphrases of the sentences found in the original document.

The automatic text summarization architecture used in this research is the Transformers architecture in [HuggingFace’s Transformers Python library](https://github.com/huggingface/transformers) (Wolf et al., 2019). Recently, the Transformers architecture is the dominant architecture used because of its ability to build high-capacity models that have gone through the training process to perform various other tasks (Fig. 2).

![T5 model tasks variety](https://miro.medium.com/max/640/0*xfXDPjASztwmJlOa.gif "T5 model tasks variety")

<sub>**Fig. 2.** T5 model tasks variety (Raffel et al., 2019)</sub>



The selection of a model that has undergone a training process is an important aspect when using HuggingFace’s Transformers for text summarization. The model chosen in this research is a Text-to-Text Transfer Transformer (T5) model trained with a collection of news items in Indonesian and Malay consisting of the original text and its summary (HuggingFace, 2020a). T5 effectively summarizes tasks with state-of-the-art performance (Raffel et al., 2019). The absence of a model that is only trained with Indonesian-language documents may be due to the limited availability of good-quality Indonesian-language documents (Ikhwantri, 2019). However, the model trained with different languages in text processing is possible to use (Pikuliak et al., 2021).

The summary process began by dividing each news item into several parts. In this case, each section is a collection of several paragraphs. This step was conducted because the T5 model can only process text with a limited length. Then, the keyword “summarize:” is inserted at the beginning of the text and then encoded into subword tokens (HuggingFace, 2020b). In the T5 model, keywords insertion is crucial to ensure that the task carried out is a summary process because this model can perform various other tasks, as shown in Fig. 2.

Afterward, the tokens obtained from the encoding process were computed by T5 to determine which subword tokens would be part of the summary. The summary stage could not be explained in detail because the Transformers architecture is an artificial neural network, which is actually a “black box,” which has limitations in explaining the possible cause and effect (Tu, 1996). The collection of computational tokens will go through a decoding process to produce a summary text of each news section. Each section summary was combined to create a complete summary. This process was carried out on all collected news items.


## Selection
At the selection stage, the news mentioning strategic responses of Indonesian companies to the COVID-19 pandemic was collected by reading the summary results of each news item. After the appropriate news data was obtained, the researcher analyzed the news summary. The researcher will read the original text if a discrepancy is found between the title and the summary results. Eventually, 225 news items are selected.

## Conclusion
To the author’s knowledge, this research is an initial study that analyzes in detail the various strategic responses of Indonesian companies to the COVID-19 pandemic crisis and identifies the patterns of shifting between strategic responses to crisis. The empirical findings of this study can confirm the strategic response to the crisis proposed by Wenzel et al. (2020), but only three of the four response strategies. The analysis of this research draws several conclusions.

First, Indonesian companies have varied approaches in implementing the three strategic responses, namely retrenchment, persevering, and innovating, as their reaction to the COVID-19 pandemic crisis. The majority of them pursued persevering as the only response. In addition, the combination of persevering and innovating is the most preferred combination of strategic responses by Indonesian companies. When viewed by the industry sector, the persevering response is also the dominant strategic response of the sector available. However, some sectors prefer innovating in the face of a pandemic, namely the health, technology, and energy sectors.

Second, several patterns of strategic response shift pursued by Indonesian companies in dealing with the COVID-19 pandemic can be identified. The results showed that regardless of what initial response was implemented to deal with the COVID-19 pandemic crisis, in the end, the majority of these companies tend to shift to persevering. However, some companies choose other strategic responses with a much smaller proportion. The study also found a pattern supporting previous studies’ argument that retrenchment can be an important precursor of other responses. These findings can broaden current knowledge about the patterns of shifting between strategic responses to crisis in general.

GitHub: [`indonesian-companies-responses-to-covid`](https://github.com/salmandazmi/indonesian-companies-responses-to-covid)

Image: [`Freepik`](https://www.freepik.com/free-photo/complex-aerial-view-city_14481994.htm#query=jakarta%20landscape&position=9&from_view=search)
