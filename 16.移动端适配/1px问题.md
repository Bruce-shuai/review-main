### 1px问题
[为什么会存在1px问题？怎么解决？](https://juejin.cn/post/7084220221505929230)


核心关键点：
1. 物理像素 (电脑设备的ppi)
2. 逻辑像素 (css中设置的px，乔布斯提出的Retina屏，即 1px逻辑像素 === 多个物理像素，这样就方便在各个类型设备上使用逻辑像素而没有兼容性问题)
3. 设备像素比dpr： 设备逻辑像素 / 物理像素    获取方法：`window.devicePixelRatio` 或 `@media screen and (-webkit-min-device-pixel-ratio: 2) {}`
4. 1像素问题：设计同学要求显示1px的物理像素
5. 最佳解决方法：伪元素制造边框，使用`transform`来改变边框大小，使用`window.devicePixelRatio`来监听是否需要进行1px大小矫正