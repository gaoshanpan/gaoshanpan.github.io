---
title: "MySQL open window function"
date: 2025-08-29 10:00:00 +0800
categories: [CS basics, Mysql] # [Top_category, sub_category]
tags: [sql]      # TAG names should always be lowercase
# author:gaoshan
---

窗口函数介绍:
    概述:
        目前先学习使用: row_number(), rank(), dense_rank(), 只要是用于做 排名 的.
        窗口函数还有很多,具体涉及到后再去深入了解

        窗口函数 => 给表 新增一列, 至于新增的一列内容是什么, 则取决于窗口函数的写法
    格式:
        开窗函数 over(partition by 分组字段 order by 排序字段 asc | desc)
    格式解释:
        开窗口函数:      这里指的是 row_number(), rank(), dense_rank()...
        over():        固定格式, 里边写的是: 分组, 排序的相关操作.
        partition by:  类似于group by
        order by:      (局部)排序

    细节: 
        1. row_number(), rank(), dense_rank()主要区别是 遇到相同数据了, 如何处理, 具体如下:
            例如: 数据为, 100, 90, 90, 80
            则:
                row_number():    1, 2, 3, 4
                rank():          1, 2, 2, 4 # 业务没要求,就选这个
                dense_rank():    1, 2, 2, 3
        2. 目前对于窗口函数, 掌握: 分组排名, 求TopN
        3. 窗口函数是MySQL8.x的新特性, 使用前要check现在mysql的版本: mysql --version