# K8s中的资源大小

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [K8s中的资源大小](#k8s中的资源大小)

<!-- /code_chunk_output -->

docker file 中可以对jvm做限制。 jvm有max和min大小，如果不限制jvm容器默认占用worker总内存的 1/4。但是如果这个数值大于约定的limit值，k8s会杀掉容器。所以一个比较好的做法是将dockerfile的jvm设为和limit一样。
request数值不能直接认为就是容器请求的大小，需要根据k8s当前worker上其他容器的情况综合考虑