---
layout: post
title: OpenVINO系列27_OpenVINO支持NLP的BERT模型.md 
tag: OpenVINO
---

对OpenVINO支持的NLP相关的模型和演示进行了一些总结，供大家学习交流参考。

# 一. 预训练模型

## 1. Intel Pre-trained Models

OpenVINO支持的`bert`相关应答模型,链接主要介绍了模型的相关描述，包括模型优点、源码框架、输入输出等信息。

- bert-large-uncased-whole-word-masking-squad-emb-0001

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_large_uncased_whole_word_masking_squad_emb_0001.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_large_uncased_whole_word_masking_squad_emb_0001.html)

- bert-large-uncased-whole-word-masking-squad-int8-0001

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_large_uncased_whole_word_masking_squad_int8_0001.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_large_uncased_whole_word_masking_squad_int8_0001.html)

- bert-small-uncased-whole-word-masking-squad-0001

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_0001.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_0001.html)

- bert-small-uncased-whole-word-masking-squad-0002

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_0002.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_0002.html)

- bert-small-uncased-whole-word-masking-squad-emb-int8-0001

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_emb_int8_0001.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_emb_int8_0001.html)

- bert-small-uncased-whole-word-masking-squad-int8-0002

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_int8_0002.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_small_uncased_whole_word_masking_squad_int8_0002.html)

## 2.Public Pre-trained Models

OpenVINO支持的命名实体识别模型

- bert-base-ner

[https://docs.openvinotoolkit.org/latest/omz_models_model_bert_base_ner.html](https://docs.openvinotoolkit.org/latest/omz_models_model_bert_base_ner.html)

# 二. demo演示

OpenVINO支持的`bert`相关应答模型,链接主要介绍了模型支持的型号，各参数信息，从模型优化到推理引擎的加载，再到模型的运行、输入输出，比较完整的演示了模型的运行流程,方便大家熟悉和理解。

- BERT Named Entity Recognition Python Demo

[https://docs.openvinotoolkit.org/latest/omz_demos_bert_named_entity_recognition_demo_python.html](https://docs.openvinotoolkit.org/latest/omz_demos_bert_named_entity_recognition_demo_python.html)

- BERT Question Answering Embedding Python Demo

[https://docs.openvinotoolkit.org/latest/omz_demos_bert_question_answering_embedding_demo_python.html](https://docs.openvinotoolkit.org/latest/omz_demos_bert_question_answering_embedding_demo_python.html)

- BERT Question Answering Python Demo

[https://docs.openvinotoolkit.org/latest/omz_demos_bert_question_answering_demo_python.html](https://docs.openvinotoolkit.org/latest/omz_demos_bert_question_answering_demo_python.html)

另外，OpenVINO还有很多好玩实用的工具等待大家玩转，比如模型评估工具benchmark_app、强大的Dl_Workbench等等。