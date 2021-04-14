# vue-fay-image

## Installation
```
npm install vue-fay-image --save
```

## Props

| 参数   |          说明          |  类型   | 默认值 |
| :----- | :--------------------: | :-----: | :----: |
| src    |        图片链接        | string  |   -    |
| width  |  宽度，默认单位为 px   | number  |  200   |
| height |  高度，默认单位为 px   | number  |  200   |
| delay  | 延迟加载时间，单位毫秒 | number  |  200   |
| lazy   |    是否开启图片懒加    | boolean | false  |

## 事件

| 事件名 |      说明      | 回调参数 |
| :----- | :------------: | :------: |
| click  | 点击图片时触发 |   src    |
