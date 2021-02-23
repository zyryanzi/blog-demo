---
title: Vue 2->3 生命周期
date: 2021-02-01 16:56:21
tags: 
    - vue3
    - vue3变更
category: 
    - 计算机科学
        - 前端
            - Vue
---

Vue2-------------------->vue3

beforeCreate　　->　　setup()

<!--more-->

created　　　　　->　　setup()

beforeMount　　　->　onBeforeMount

mounted　　　　　->　onMounted

beforeUpdate　　->　　onBeforeUpdate

updated　　　　　->　　onUpdated

beforeDestroy　　->　　onBeforeUnmount

destroyed　　　　->　　onUnmounted

activated　　　　->　　onActivated

deactivated　　　-> 　onDeactivated

errorCaptured　　-> 　onErrorCaptured

