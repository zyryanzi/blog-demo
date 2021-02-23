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

*最近了解了一下vue3，虽然连vue2都不太理解，记录一下也算是为自己打打气*
<!--more-->

Vue2-------------------->vue3

beforeCreate　　->　　setup()

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

