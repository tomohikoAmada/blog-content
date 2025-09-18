---
title: "AvailableAlgoParams"
date: 2025-09-18
category: "量化交易"
tags: ["TWS API", "C++"]
draft: false
---

# AvailableAlgoParams

这个API设计允许交易者详细控制算法交易的执行方式，包括时间控制、价格控制、数量控制等多个维度。要使用这些功能，需要先创建一个基础订单(Order)对象，然后调用相应的Fill\*Params函数来设置算法参数。 需要注意的是，使用这些算法交易功能可能需要特定的权限和订阅级别，建议在使用前查看IB的相关文档和权限要求。

```cpp
/* Copyright (C) 2019 Interactive Brokers LLC. All rights reserved. This code is subject to the terms
 * and conditions of the IB API Non-Commercial License or the IB API Commercial License, as applicable. */


#pragma once
#ifndef TWS_API_SAMPLES_TESTCPPCLIENT_AVAILABLEALGOPARAMS_H
#define TWS_API_SAMPLES_TESTCPPCLIENT_AVAILABLEALGOPARAMS_H


#include <string>


struct Order;   // 前向声明Order结构体，避免循环依赖


/**
* @class AvailableAlgoParams
* @brief 提供设置各种算法交易参数的工具类
* 
* 这个类包含了一系列静态方法，用于配置不同类型的算法交易策略参数。
* 每个方法都会修改传入的Order对象，添加相应的算法参数。
*/
class AvailableAlgoParams {
public:
    // 以下是一系列静态成员函数，用于填充不同类型的算法交易参数


    /**
     * @brief 配置到达价格(Arrival Price)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param maxPctVol 最大参与量百分比(0.0-100.0)
     * @param riskAversion 风险规避级别("Get Done"/"Aggressive"/"Neutral"/"Passive")
     * @param startTime 算法开始时间(格式: YYYYMMDD-HH:MM:SS)
     * @param endTime 算法结束时间(格式: YYYYMMDD-HH:MM:SS)
     * @param forceCompletion 是否强制在结束时间前完成
     * @param allowPastTime 是否允许超过结束时间继续执行
     */
    // 填充到达价格算法的参数
    static void FillArrivalPriceParams(Order& baseOrder, double maxPctVol, std::string riskAversion, std::string startTime, std::string endTime, bool forceCompletion, bool allowPastTime);


    /**
     * @brief 配置Dark Ice算法参数
     * @details Dark Ice算法通过显示较小的订单量来隐藏大订单，减少市场冲击
     * 
     * @param baseOrder 要配置的订单对象
     * @param displaySize 每次显示的订单数量
     * @param startTime 算法开始时间
     * @param endTime 算法结束时间
     * @param allowPastEndTime 是否允许超过结束时间
     */
    // 填充Dark Ice算法的参数
    static void FillDarkIceParams(Order& baseOrder, int displaySize, std::string startTime, std::string endTime, bool allowPastEndTime);
    
    /**
     * @brief 配置百分比成交量(Percentage of Volume)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param pctVol 目标成交量百分比
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param noTakeLiq 是否避免消耗市场流动性
     */
    // 填充百分比成交量算法的参数
    static void FillPctVolParams(Order& baseOrder, double pctVol, std::string startTime, std::string endTime, bool noTakeLiq);
    
    /**
     * @brief 配置时间加权平均价格(TWAP)算法参数
     * @details TWAP算法在指定时间段内均匀执行订单
     * 
     * @param baseOrder 要配置的订单对象
     * @param strategyType 策略类型
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param allowPastEndTime 是否允许超过结束时间
     */
    // 填充时间加权平均价格(TWAP)算法的参数
    static void FillTwapParams(Order& baseOrder, std::string strategyType, std::string startTime, std::string endTime, bool allowPastEndTime);
    
    /**
     * @brief 配置成交量加权平均价格(VWAP)算法参数
     * @details VWAP算法根据历史成交量模式执行订单
     * 
     * @param baseOrder 要配置的订单对象
     * @param maxPctVol 最大参与量百分比
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param allowPastEndTime 是否允许超过结束时间
     * @param noTakeLiq 是否避免消耗流动性
     * @param speedUp 是否加速执行
     */
    // 填充成交量加权平均价格(VWAP)算法的参数
    static void FillVwapParams(Order& baseOrder, double maxPctVol, std::string startTime, std::string endTime, bool allowPastEndTime, bool noTakeLiq, bool speedUp);
    
    /**
     * @brief 配置积累分配(Accumulate/Distribute)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param componentSize 子订单大小
     * @param timeBetweenOrders 子订单间隔时间(秒)
     * @param randomizeTime20 是否随机化时间(±20%)
     * @param randomizeSize55 是否随机化大小(±55%)
     * @param giveUp 放弃执行的阈值
     * @param catchUp 是否追赶未完成的订单
     * @param waitForFill 是否等待成交
     * @param startTime 开始时间
     * @param endTime 结束时间
     */
    // 填充积累分配算法的参数
    static void FillAccumulateDistributeParams(Order& baseOrder, int componentSize, int timeBetweenOrders, bool randomizeTime20, bool randomizeSize55, int giveUp, bool catchUp, bool waitForFill, std::string startTime, std::string endTime);
    
    /**
     * @brief 配置平衡影响风险(Balance Impact Risk)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param maxPctVol 最大参与量百分比
     * @param riskAversion 风险规避级别
     * @param forceCompletion 是否强制完成
     */
    // 填充平衡影响风险算法的参数
    static void FillBalanceImpactRiskParams(Order& baseOrder, double maxPctVol, std::string riskAversion, bool forceCompletion);
    
    /**
     * @brief 配置最小影响(Minimum Impact)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param maxPctVol 最大参与量百分比
     */
    // 填充最小影响算法的参数
    static void FillMinImpactParams(Order& baseOrder, double maxPctVol);
    
    /**
     * @brief 配置自适应(Adaptive)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param priority 执行优先级("Normal"/"Urgent"/"Patient")
     */
    // 填充自适应算法的参数
    static void FillAdaptiveParams(Order& baseOrder, std::string priority);
    
    /**
     * @brief 配置收盘价(Close Price)算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param maxPctVol 最大参与量百分比
     * @param riskAversion 风险规避级别
     * @param startTime 开始时间
     * @param forceCompletion 是否强制完成
     */
    // 填充收盘价算法的参数
    static void FillClosePriceParams(Order& baseOrder, double maxPctVol, std::string riskAversion, std::string startTime, bool forceCompletion);
    
    /**
     * @brief 配置价格变动百分比成交量算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param pctVol 目标成交量百分比
     * @param deltaPctVol 成交量变化百分比
     * @param minPctVol4Px 最小价格对应的成交量百分比
     * @param maxPctVol4Px 最大价格对应的成交量百分比
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param noTakeLiq 是否避免消耗流动性
     */
    // 填充价格变动百分比成交量算法的参数
    static void FillPriceVariantPctVolParams(Order baseOrder, double pctVol, double deltaPctVol, double minPctVol4Px, double maxPctVol4Px, std::string startTime, std::string endTime, bool noTakeLiq);
    
    /**
     * @brief 配置规模变动百分比成交量算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param startPctVol 起始成交量百分比
     * @param endPctVol 结束成交量百分比
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param noTakeLiq 是否避免消耗流动性
     */
    // 填充规模变动百分比成交量算法的参数
    static void FillSizeVariantPctVolParams(Order baseOrder, double startPctVol, double endPctVol, std::string startTime, std::string endTime, bool noTakeLiq);
    
    /**
     * @brief 配置时间变动百分比成交量算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param startPctVol 起始成交量百分比
     * @param endPctVol 结束成交量百分比
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param noTakeLiq 是否避免消耗流动性
     */
    // 填充时间变动百分比成交量算法的参数
    static void FillTimeVariantPctVolParams(Order baseOrder, double startPctVol, double endPctVol, std::string startTime, std::string endTime, bool noTakeLiq);
    
    /**
     * @brief 配置Jefferies VWAP算法参数
     * 
     * @param baseOrder 要配置的订单对象
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param relativeLimit 相对限价
     * @param maxVolumeRate 最大成交量比率
     * @param excludeAuctions 排除的拍卖类型
     * @param triggerPrice 触发价格
     * @param wowPrice WOW价格
     * @param minFillSize 最小成交量
     * @param wowOrderPct WOW订单百分比
     * @param wowMode WOW模式
     * @param isBuyBack 是否回购
     * @param wowReference WOW参考
     */
    // 填充Jefferies VWAP算法的参数
    static void FillJefferiesVWAPParams(Order baseOrder, std::string startTime, std::string endTime, double relativeLimit, double maxVolumeRate, std::string excludeAuctions, double triggerPrice, double wowPrice, int minFillSize, double wowOrderPct, std::string wowMode, bool isBuyBack, std::string wowReference);
    
    /**
     * @brief 配置CSFB Inline算法参数
     * @details 必须直接路由到"CSFBALGO"
     * 
     * @param baseOrder 要配置的订单对象
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param execStyle 执行风格
     * @param minPercent 最小百分比
     * @param maxPercent 最大百分比
     * @param displaySize 显示数量
     * @param auction 拍卖类型
     * @param blockFinder 是否启用大宗交易查找
     * @param blockPrice 大宗交易价格
     * @param minBlockSize 最小大宗交易规模
     * @param maxBlockSize 最大大宗交易规模
     * @param iWouldPrice "I Would"价格
     */
    // 填充CSFB Inline算法的参数
    static void FillCSFBInlineParams(Order baseOrder, std::string startTime, std::string endTime, std::string execStyle, int minPercent,int maxPercent, int displaySize, std::string auction, bool blockFinder, double blockPrice, int minBlockSize, int maxBlockSize, double iWouldPrice);
};


#endif
```