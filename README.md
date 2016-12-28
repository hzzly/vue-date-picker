# vue-datetime-picker
a vue datetime picker component

日期时间段选择 [传送门](http://hjingren.cn/mydemo/datepickerv2/dist/)

![date-pic](https://github.com/hzzly/vue-datetime-picker/blob/master/src/assets/date-pic.png) 

![confim-pic](https://github.com/hzzly/vue-datetime-picker/blob/master/src/assets/confim-pic.png)


  - 开工和完工时间在一个控件中完成点击上方箭头，可变换月份；
  - 默认值为：开工在后天，完工在第六天；


###规则：
  - 今天以前不可选择；
  - 点击某个日子，则将最近的节点移动过去；
  - 如果离两个节点一样，则将开工移动过去;
  - 两个节点也可选到1天里；显示为各一半

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```
