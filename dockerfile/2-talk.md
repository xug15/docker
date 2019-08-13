# Talk contents
[Back home](../RREADME.md)

[华为云gcs说明](https://support.huaweicloud.com/tr-gcs/gcs_tr_01_0005.html)
* 一个流程，里面有些步骤要的资源多，有些步骤那资源少，你如果使用同样的镜像，那么你的这个资源大小就很难控制
* 还有dockerstore，ga4gh什么等社区
* 流程，我建议可以先参考一下next flow里面的一些sample流程
* 这个领域，跟gcs差不多的东西有：cromwell一个，还有一个是nextflow
* 但是那些都是国外主导的，我们要做好，成为国内标准。所以你这边其实要有一个第一批元老一起打造下一代生信流程引擎的“使命”，哪里值得改进都可以一起讨论。
* https://github.com/nextflow-io/awesome-nextflow
* GCS的目标是测序厂商，不是普通消费者。 但是这次和清华合作，不是为了赚钱，而是为了推广生态标准，迁移测序厂商，愿意使用GCS这种流程语法模式，并使之成为一种规范。
* 所以你要发布的流程，需要跟有吸引力。 一个容器包含所有的工具，这种看着好像不太有吸引力。你可以先看看刚才我发的链接，这个跟咱们这次的目标比较像。
* 或者你直接挑几个主流的流程，翻译一下（如果能够做个工具，自动翻译那就更牛逼了）成为GCS的语法，先发布看看效果也不错
* https://github.com/kubegene/kubegene/tree/master/example
* 这个是GCS对应的开源项目，你可以认为这个是和 nextflow 直接竞争的就完了
* 有一个点是肯定的，GCS或者KubeGene 是绑定 kubernetes的，这个是因为我们认准了这个趋势。
* 事实上，kubernetes已经是容器编排的标准了
* https://mp.weixin.qq.com/s/SaU3mMg8XPu6Rj-xDCAOJQ
* 如果走公有云的话，直接写完GCS流程，在华为云GCS上面跑就行了。 但是有些人不是要有私有云环境的嘛，比如清华这边有自己的机房，要想也能跑相同的流程。就需要自己搭建k8s就行了。有了k8s就能跑相同的流程了
* 一起打造一个亚洲版的 Broad Institute [色]
* 是的，大部分的生信软件，在dockerhub都有，还有dockersotre
* 可以的，那就 直接走 kubegene 那条路会方便点。
* 请问[Minikube for KubeGene](https://kubegene.io/docs/started/getting-started-minikube/)

