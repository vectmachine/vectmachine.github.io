---
title: CSCPaperreading
commentable: flase
Edit: 2022-07-21
mathjax: true
mermaid: true
tags: CSC NLP paper
categories: NLP  
description: 
" A Hybrid Approach to Automatic Corpus Generation " and FASPell
---

# A Hybrid Approach to Automatic Corpus Generation-2018
- *Motivation:* 当前的CSC对混淆集合具有很强依赖，本文从视觉和语音出发，基于OCR和ASR技术构造混淆集

- *Related work：* 
  - 目前暂无大规模注释集的相关工作
  - Phonological and Logographic Influences on Errors in Written Chinese Words 
  - Automatic Detection and Correction for Chinese Misspelled Words Using Phonological and Orthographic Similarities :以上两篇指出错误主要由视觉与语音引起，且语音错误大约是视觉引起错误的两倍

# FASPell: A Fast, Adaptable, Simple, Powerful Chinese Spell Checker Based On DAE-Decoder Paradigm
- *Motivation:* 曾经的CSC工作几乎都采用了相同的工作范式：检错，从候选集中选出最适合的，但是这种范式存在显著缺陷：
  - 效果严重依赖于训练集的质量，当质量不高时易过拟合。“A Hybrid Approach to Automatic Corpus Generation”中提出的自动生成混淆集，效果存在一定上限
  - 混淆集在汉字相似度上利用不高，且不灵活。
    - 针对不同场景的混淆集效果不一样，极端的例子如繁体混淆集在简体表现不好
    - 不同汉字的相似度应该不一样，而混淆集中没有进一步区分。如果添加规则进行进一步细化，则增加许多不必要的噪声
    
- *Approach：* 本文提出了一种编码器——解码器的范式，编码器利用无监督预训练的方法减少对训练集的依赖；解码器有助于减少对于混淆集的依赖，更加充分的利用汉字的相似性特征

- *Related work：* 
  - 目前暂无大规模注释集的相关工作
  - A Hybrid Approach to Automatic Corpus Generation：混淆集的自动生成；指出“ confusing characters for machines are different from those for humans”，实际应用上很可能出现混淆集中没有答案
