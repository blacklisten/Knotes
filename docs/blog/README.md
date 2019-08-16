# 1. JS计算两个时间间隔

## 1 前言
### 1.1 业务场景
JavaScript计算两个时间相隔了 多少年多少月多少日。时分秒这里不作考虑。

## 2 实现原理
### 2.1 获取当前时间
若需要和当前时间比较，这里提供当前时间的获取格式化方法。我这里是根据传入类型输出日期或者日期+时间，可以明显看出`type`为`day`时，输出日期。
```
getNowDate(type){
    let now = new Date()
    let year = now.getFullYear()
    let month = now.getMonth() + 1
    let day = now.getDate()
    let hh = now.getHours()
    let mm = now.getMinutes()
    let ss = now.getSeconds()

    month = month < 10 ? '0'+ month : month
    day = day < 10 ? '0'+ day : day

    if(type == 'day'){
      return year +'-'+ month +'-'+ day
    } else {
      return year +'-'+ month +'-'+ day + ' '+ hh + ':' + mm + ':' + ss
    }
},
```
### 2.2比较时间
传入两个参数，格式如：2008-08-08

```
getDiffYmdBetweenDate(sDate1,sDate2){
    var fixDate = function(sDate){
        var aD = sDate.split('-');
        for(var i = 0; i < aD.length; i++){
            aD[i] = fixZero(parseInt(aD[i]));
        }
        return aD.join('-');
    };
      
    var fixZero = function(n){
        return n < 10 ? '0'+n : n;
    };
      
    var fixInt = function(a){
        for(var i = 0; i < a.length; i++){
            a[i] = parseInt(a[i]);
        }
        return a;
    };
      
    var getMonthDays = function(y,m){
        var aMonthDays = [0,31,28,31,30,31,30,31,31,30,31,30,31];
        if((y%400 == 0) || (y%4==0 && y%100!=0)){
            aMonthDays[2] = 29;
        }
        return aMonthDays[m];
    };
      
    var checkDate = function(sDate){
    };
    var y = 0;
    var m = 0;
    var d = 0;
    var sTmp;
    var aTmp;
    sDate1 = fixDate(sDate1);
    sDate2 = fixDate(sDate2);
    if(sDate1 > sDate2){
        return 'past'
    }
    var aDate1 = sDate1.split('-');
    aDate1 = fixInt(aDate1);
    var aDate2 = sDate2.split('-');
    aDate2 = fixInt(aDate2);
    //计算相差的年份
    /*aTmp = [aDate1[0]+1,fixZero(aDate1[1]),fixZero(aDate1[2])];
    while(aTmp.join('-') <= sDate2){
        y++;
        aTmp[0]++;
    }*/
    y = aDate2[0] - aDate1[0];
    if( sDate2.replace(aDate2[0],'') < sDate1.replace(aDate1[0],'')){
        y = y - 1;
    }
    //计算月份
    aTmp = [aDate1[0]+y,aDate1[1],fixZero(aDate1[2])];
    while(true){
        if(aTmp[1] == 12){
            aTmp[0]++;
            aTmp[1] = 1;
        }else{
            aTmp[1]++;
        }
        if(([aTmp[0],fixZero(aTmp[1]),aTmp[2]]).join('-') <= sDate2){
            m++;
        } else {
            break;
        }
    }
    //计算天数
    aTmp = [aDate1[0]+y,aDate1[1]+m,aDate1[2]];
    if(aTmp[1] > 12){
        aTmp[0]++;
        aTmp[1] -= 12;
    }
    while(true){
        if(aTmp[2] == getMonthDays(aTmp[0],aTmp[1])){
            aTmp[1]++;
            aTmp[2] = 1;
        } else {
            aTmp[2]++;
        }
        sTmp = ([aTmp[0],fixZero(aTmp[1]),fixZero(aTmp[2])]).join('-');
        if(sTmp <= sDate2){
            d++;
        } else {
            break;
        }
    }
    return {y:y,m:m,d:d}
},
```
返回的结果是一个对象，包含了`y m d`三个属性，可根据业务进行取用展示。

比较时间的时候，若`sDate1`为小的时间，这里直接返回`past`。
### 2.3 页面展示
这里根据业务不同可以作不同的展示。这里列出自己的Vue展示处理展示。

其中`this.dataForm.hasdata`为页面的v-model。这样的处理的效果是观看直观。

```
let now = this.getNowDate('day')
let diffDate = this.getDiffYmdBetweenDate(now,this.data)
let hasdata = ''

if(diffDate == 'past'){
    this.dataForm.hasdata = '已过期'
} else {
    if(diffDate.y > 0){
        hasdata += diffDate.y + '年' + diffDate.m + '月'
    } else if (diffDate.y == 0) {
        if(diffDate.m > 0){
            hasdata += diffDate.m + '月'
        }
    }
    this.dataForm.hasdata = hasdata + diffDate.d + '日'
}
```
### 2.4 页面效果

![](https://github.com/xrkffgg/Kimg/blob/master/blog/01-1.png?raw=true)
![](https://github.com/xrkffgg/Kimg/blob/master/blog/01-2.png?raw=true)
![](https://github.com/xrkffgg/Kimg/blob/master/blog/01-3.png?raw=true)

## 3 后记
**感谢支持。若不足之处，欢迎大家指出，共勉。**

**如果觉得不错，记得 点赞 谢谢大家** 😂

### 3.1 参考资料
- [www.itkeyword.com/doc/...](http://www.itkeyword.com/doc/3825817034839276x780)

### 3.2 时间处理js库
这里列举几个js库备查找使用，排序无意义。

- moment
- day
- date-fns
- miment