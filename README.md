# 软件项目研发实践——实验3（实现基本的图像分类APP）

## 一、项目预备工作
- 1.使用git clone https://github.com/hoitab/TFLClassify.git 下载初始代码
- ![image1](https://github.com/Shawpromax/images/blob/main/test3_1.png)
- 2.调试项目，进行build gradle
- ![image2](https://github.com/Shawpromax/images/blob/main/test3_2.png)
- 3.连接手机并进行相关调试

## 二、完善项目
- 1.向应用中添加TensorFlow Lite
- 2.选择已经下载的自定义的训练模型。本教程模型训练任务以后完成，这里选择finish模块中ml文件下的FlowerModel.tflite
- ![image3](https://github.com/Shawpromax/images/blob/main/test3_3.png)
- 3.完成模型导入，系统将自动下载模型的依赖包并将依赖项添加至模块的build.gradle文件
- ![image4](https://github.com/Shawpromax/images/blob/main/test3_4.png)
- 4.查看TODO列表视图，方便查看（进行group by分组）
- ![image5](https://github.com/Shawpromax/images/blob/main/test3_5.png)
- 5.在模块MainActivity.kt文件的TODO 1，添加初始化训练模型的代码
```
    private class ImageAnalyzer(ctx: Context, private val listener: RecognitionListener) :
        ImageAnalysis.Analyzer {

        // TODO 1: Add class variable TensorFlow Lite Model
        // Initializing the flowerModel by lazy so that it runs in the same thread when the process
        // method is called.
        private val flowerModel = FlowerModel.newInstance(ctx)
```
- 6.在CameraX的analyze方法内部，需要将摄像头的输入ImageProxy转化为Bitmap对象，并进一步转化为TensorImage 对象
```
override fun analyze(imageProxy: ImageProxy) {

            val items = mutableListOf<Recognition>()

            // TODO 2: Convert Image to Bitmap then to TensorImage
            val tfImage = TensorImage.fromBitmap(toBitmap(imageProxy))
```
- 7.对图像进行处理并生成结果，主要包含下述操作(按照属性score对识别结果按照概率从高到低排序,列出最高k种可能的结果，k的结果由常量MAX_RESULT_DISPLAY定义)
```
            // TODO 3: Process the image using the trained model, sort and pick out the top results
            val outputs = flowerModel.process(tfImage)
                .probabilityAsCategoryList.apply {
                    sortByDescending { it.score } // Sort with highest confidence first
                }.take(MAX_RESULT_DISPLAY) // take the top results
```
- 8.将识别的结果加入数据对象Recognition 中，包含label和score两个元素。后续将用于RecyclerView的数据显示
```
            // TODO 4: Converting the top probability items into a list of recognitions
            for (output in outputs) {
                items.add(Recognition(output.label, output.score))
            }
```
- 9.添加用于虚拟显示识别结果的代码(将原先的代码注释或删除)
```
for (i in 0..MAX_RESULT_DISPLAY-1){
    items.add(Recognition("Fake label $i", Random.nextFloat()))
}
```
- 10.重新运行start模块，在手机中安装并运行应用

## 三、最终运行效果如下（识别花朵为玫瑰花）
- ![image6](https://github.com/Shawpromax/images/blob/main/test3_6.png)













