---
title: SKU算法总结分享
date: 2016-04-26 16:28:49
tags: [sku,算法,随笔]
---
#### 1. SKU简介

下面是SKU来自维基百科的解释：
>**最小存货单位**（Stock Keeping Unit, SKU）是一个会计学名词，定义为保存库存控制的最小可用单位，例如纺织品中一个SKU通常表示规格、颜色、款式，而在连锁零售门店中有时称单品为一个SKU。

直观一点的实际应用，可以参考淘宝的商品SKU选择界面，如下：

![](http://7xth8v.com2.z0.glb.qiniucdn.com/image/sku.png)              

#### 2. 基础数据

```language-javascript
	//属性集
    var keys = [
        ['10'],  //iPhone7s
        ['20','21','22','23','24'],  //土豪金,白色,黑色,灰色,粉色
        ['30','31','32','33','34','35','36','37','38'],  //港版，大陆行货，亚太版，美版，韩版，日版，意大利版，俄罗斯版，欧版
        ['40'] //4.7寸
    ];
```

```language-javascript
    //后台读取结果集
    var data = {
        "10;24;31;40":{"price":366,"count":46},
        "10;24;33;40":{"price":416,"count":77},
        "10;24;34;40":{"price":456,"count":9},
        "10;24;35;40":{"price":371,"count":33},
        "10;24;36;40":{"price":411,"count":79},
        "10;24;37;40":{"price":421,"count":87},
        "10;24;38;40":{"price":461,"count":9},
        "10;23;32;40":{"price":406,"count":9},
        "10;23;33;40":{"price":416,"count":90},
        "10;23;36;40":{"price":411,"count":90},
        "10;23;37;40":{"price":421,"count":10},
        "10;23;38;40":{"price":461,"count":9},
        "10;22;35;40":{"price":361,"count":25},
        "10;21;33;40":{"price":416,"count":10},
        "10;21;38;40":{"price":461,"count":80},
        "10;21;30;40":{"price":356,"count":43},
        "10;20;31;40":{"price":356,"count":46},
        "10;20;32;40":{"price":396,"count":49},
        "10;20;35;40":{"price":361,"count":34},
        "10;20;36;40":{"price":401,"count":41},
        "10;20;30;40":{"price":346,"count":3}
    };
```

#### 3. 算法分析

    重点在于排列组合出所有的属性集，和后台读取的结果集进行比对；

#### 4. 代码实现
```language-javascript
/*
 * sku算法
 * by xqs 16.08.11
 * */

define(function (require, exports, module) {

    'use strict';

    //按钮样式class
    var className = {
        sku: 'sku-key',
        active: 'active',
        disabled: 'ban'
    };

    //sku对象
    var sku = {
        delimiter: 'b'      //分隔符
    };


    //初始化，组合新的结果集
    sku.init = function (container) {

        console.time('skuInit time');

        sku.container = $(container);  //存储skuData的外容器

        var data = $(container).data('sku-map');
        if (!data || sku.container.data('initialized')) return;
        //console.log(JSON.stringify(data))

        var skuMap = sku.skuMap = {};  //sku结果集

        //拿到已有skuMap中所有的sku组合key
        var skuKeys = $.map(data, function (val, key) {
            if (!val.count) val.count = 0;
            return key;
        });
        //console.log(skuKeys, skuMap)

        //转换成数组后，排列组合计算出所有的可能，存储在skuMap中
        $.each(skuKeys, function (i, v) {
            var keyArr = v.split(sku.delimiter).sort(function (a, b) {
                    return parseInt(a) - parseInt(b);
                }),
                combArr = sku.combineArr(keyArr);
            //console.log(JSON.stringify(combArr))

            var skuInfo = data[v];
            $.each(combArr, function (j, k) {
                if (skuMap[k]) {
                    skuMap[k].count += skuInfo.count;
                    skuMap[k].prices.push(skuInfo.price);
                    $.unique(skuMap[k].prices);  //数组去重
                } else {
                    skuMap[k] = {
                        count: skuInfo.count,
                        prices: [skuInfo.price]
                    }
                }
            });

            //已有的skuMap中的信息存储到skuMap中,替换原有的key
            skuMap[keyArr.join(sku.delimiter)] = $.extend(null, {
                count: skuInfo.count,
                prices: [skuInfo.price]
            }, skuInfo);

        });

        sku.container.data('initialized', true);

        //打印一下sku字典拼装花费的时间
        console.timeEnd('skuInit time');

        console.info(JSON.stringify(skuMap));


        //点击sku选项
        var showPrice, showPic, skuDesc,
            defaultPrice = $('.sku-price').text(),
            defaultPic = $('.sku-pic').attr('src'),
            defaultDesc = '请选择' + $.map(sku.container.find('dl dt'), function (item) {
                    return $(item).text();
                }).join(',');

        //初始化渲染
        $('.sku-desc').html(defaultDesc);

        //未选择之前，先检查一下状态
        sku.container.find('.' + className.sku).each(function () {
            //先初始化一下，看看是否所有的按钮都可选
            var $this = $(this);
            if (!skuMap[$this.data('value')]) {
                $this.addClass(className.disabled).removeClass(className.active);
            } else {
                $this.removeClass(className.disabled);
            }
        }).end().off('click').on('click', '.' + className.sku, function () {
            //添加click事件
            var $this = $(this);

            if ($this.hasClass(className.disabled)) return false;

            //选中自己，取消兄弟元素选中状态
            $this.toggleClass(className.active).siblings().removeClass(className.active);

            //已选择的sku按钮信息
            var skuSelected = sku.selected();
            if (skuSelected.keys.length) {
                var skuSelectedKey = skuSelected.keys.join(sku.delimiter);
                //console.info('skuKey==', skuSelectedKey);

                //显示价格区间及sku描述等信息
                var minPrice = Math.min.apply(null, skuMap[skuSelectedKey].prices),
                    maxPrice = Math.max.apply(null, skuMap[skuSelectedKey].prices);

                showPrice = minPrice === maxPrice ? ['￥', maxPrice].join('') : ['￥', minPrice, '-', maxPrice].join('');
                skuMap[skuSelectedKey].itemPic && (showPic = skuMap[skuSelectedKey].itemPic);
                skuDesc = $.map(skuSelected.desc, function (v, i) {
                    return '<span>' + v + '</span>';
                }).join('');

                //限购数量
                var limitCount = skuSelected.detail && skuSelected.detail.limit ? skuSelected.detail.limit : null;
                sku.container.siblings('.quantity').find('.number').data('max', limitCount).spinner();  //改变数量控制;

                //验证其他sku按钮
                var $skuOthers = $('.' + className.sku).not('.' + className.active).not($this);
                $.each($skuOthers, function () {
                    var $that = $(this), $sibSelected = $that.siblings('.' + className.active);
                    var othersArr = [];
                    if ($sibSelected[0]) {
                        //同级别的sku已有选择，则在新数组中取出
                        othersArr = $.grep(skuSelected.keys, function (v, i) {
                            return v != $sibSelected.data('value');
                        });
                    } else {
                        othersArr = skuSelected.keys.concat();
                    }
                    othersArr = othersArr.concat($that.data('value')).sort(function (a, b) {
                        return parseInt(a) - parseInt(b);
                    });

                    //console.info('othersArr==', othersArr.join(sku.delimiter));

                    if (!skuMap[othersArr.join(sku.delimiter)]) {
                        $that.addClass(className.disabled);
                    } else {
                        $that.removeClass(className.disabled);
                    }

                });

            } else {
                //设置默认价格、描述
                showPrice = defaultPrice;
                showPic = defaultPic;
                skuDesc = defaultDesc;
                $.each($('.' + className.sku), function () {
                    var $this = $(this);
                    if (!skuMap[$this.data('value')]) {
                        $this.addClass(className.disabled).removeClass(className.active);
                    } else {
                        $this.removeClass(className.disabled);
                    }
                })
            }

            //显示价格和图片等信息
            $('.sku-price').text(showPrice);
            $('.sku-pic').attr('src', showPic);
            $('.sku-desc').html(skuDesc);

        });

        //只有一组sku的话，直接选中
        if ($.map(data, function (v, k) {
                return k;
            }).length === 1) {
            sku.container.find('.' + className.sku).trigger('click');
        }

    };

    //数组排列组合算法
    sku.combineArr = function (arr) {
        //console.log('arr', arr)
        var len = arr.length, result = [];
        combine();
        function combine(item, index) {
            item = item || '';
            index = index || 0;
            if (index == len)return;
            for (var i = index; i < len; i++) {
                var newItem = (item ? [item, sku.delimiter, arr[i]] : [item, arr[i]]).join('');
                result.push(newItem);
                combine(newItem, ++index);
            }
        }

        return result;
    };


    //获取已选中的sku信息
    sku.selected = function () {
        var skuMap = sku.skuMap;
        var $skuSelected = $('.' + className.sku + '.' + className.active);
        var result = {
            keys: []
        };
        if ($skuSelected[0]) {
            result.keys = $.map($skuSelected, function (v, i) {
                return $(v).data('value');
            }).sort(function (a, b) {
                return parseInt(a) - parseInt(b);
            });

            result.desc = $.map($skuSelected, function (v, i) {
                return $(v).text();
            });

            if (skuMap[result.keys.join(sku.delimiter)].itemId) {
                result.detail = skuMap[result.keys.join(sku.delimiter)];
                result.itemId = result.detail.itemId;
            }

        }

        return result;
    };


    module.exports = sku;


});

```

#### 5. demo演示
![](http://7xth8v.com2.z0.glb.qiniucdn.com/image/sku_02.png)
