---
title: 辅助编程应用
date: 2024-12-29
author: 付辉

---

有下面的开源模型供参考：
1. Salesforce AI Research发布的Codegen
2. Llama Code
3. Stability AI公司发布的Stable Code 3B模型，代码补齐模型

模型的渐进式生成方案：按照 max_new_token 设定的长度限制，先生成一小段内容，推送给客户端显示，然后将之前生成的全部内容作为参数，继续调用模型的generate方法生成接下来的内容。

> ：如果按照这种渐进式的方案，前端的展示需要一个框架来处理