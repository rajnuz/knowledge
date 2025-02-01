# ingress 配置

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [ingress 配置](#ingress-配置)
  - [安装ingress](#安装ingress)
  - [rewrite](#rewrite)

<!-- /code_chunk_output -->


使用ingress nginx

## 安装ingress

从[ingress-nginx](https://github.com/kubernetes/ingress-nginx)下载最新的helm chart

注意修改values.yaml中的`allowSnippetAnnotations` 设为true。这个修改是为了使用ingress-nginx的configuration-snippet annotaion来注入一些nginx原生的配置

## rewrite

参考[link](https://support.huaweicloud.com/usermanual-cce/cce_10_0696.html)

有些场景下我们希望location中path不要影响服务本身配置，典型例子是在springboot中path的内容会修改context-path上。例如application.yml中的内容是：server.servlet.context-path是/api，而ingress中配置的path是/svc1。k8s上的服务访问的path是/svc1/api

如果我们不希望部署影响代码本身，我们需要rewrite请求

rewrite的第一种方式是配置使用rewrite-target注解，这个注解会影响同一个ingress文件里的所有配置,所以对于不需要rewrite的服务不要写在用一个ingress文件下面：

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress                   
metadata:    
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"networking.k8s.io/v1","kind":"Ingress","metadata":{"annotations":{"kubernetes.io/ingress.class":"nginx","nginx.ingress.kubernetes.io/configuration-snippet":"set $p
    kubernetes.io/ingress.class: nginx                                                                                                                                                  
    nginx.ingress.kubernetes.io/configuration-snippet: |
      set $prefix '';                                   
      if ($request_uri ~* "^/([^/]+)") {
        set $prefix $1;                 
      }                
      proxy_set_header X-Forwarded-Prefix /$prefix;
    nginx.ingress.kubernetes.io/rewrite-target: /api/$2
  creationTimestamp: "2024-05-09T05:44:38Z"            
  generation: 4                            
  name: nih-excel
  namespace: nih 
  resourceVersion: "299929"
  uid: bd72230a-d830-4084-b0b6-8f2f73f23d6f
spec:                                      
  rules:
  - host: nih.local
    http:          
      paths:
      - backend:
          service:
            name: nih-excel
            port:          
              number: 31068
        path: /nihnrexcel(/|$)(.*)
        pathType: Prefix          
      - backend:        
          service:
            name: nihnr
            port:      
              number: 30710
        path: /nihnr(/|$)(.*)
        pathType: Prefix     
status:                 
  loadBalancer:
    ingress:   
    - ip: 192.168.42.129
```

rewrite的第二个配置方式是将rewrite配置在configuration-snippet中，这样的好处是可以针对每一个location来之写，也可以将不需要rewrite的服务也配置在同一个ingress中：

```yaml
apiVersion: networking.k8s.io/v1                                                          
kind: Ingress                         
metadata:
  annotations:                  
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"networking.k8s.io/v1","kind":"Ingress","metadata":{"annotations":{"kubernetes.io/ingress.class":"nginx","nginx.ingress.kubernetes.io/configuration-snippet":"set $prefix '';
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/configuration-snippet: |
      set $prefix '';                 
      if ($request_uri ~* "^/nihnr/([^/]+)") {                
        set $prefix $1;
      }                                                
      proxy_set_header X-Forwarded-Prefix /nihnr/$prefix;                                                                                                                                     
      rewrite ^/nihnr/excel/(.*)$ /api/$1 break;        
      rewrite ^/nihnr/(.*)$ /$1 break;                                                                              
  creationTimestamp: "2024-05-10T03:29:23Z"                                                                                                                        
  generation: 2                                                                   
  name: nih-excel
  namespace: nih                           
  resourceVersion: "313553"
  uid: 763234c4-b0b9-4097-a99c-78820c74238f
spec:           
  rules:                   
  - host: nih.local                        
    http:
      paths:
      - backend:   
          service:
            name: nih-excel
            port:
              number: 31068
        path: /nihnr/excel 
        pathType: Prefix
      - backend:           
          service:        
            name: nihnr 
            port:
              number: 30710
        path: /nihnr   
        pathType: Prefix
      - backend:           
          service:  
            name: nih-kpi
            port:
              number: 32557
        path: /api       
        pathType: Prefix

```

需要注意的是，在使用swagger的时候，如果只配置rewrite，我们可以在浏览器中看到swagger下发的请求其实是rewrite后的路径。例如我将/nih/excel rewrite为/svc，swagger下发的请求就是/svc的。此时如果整个ingress中没有/svc的path，swagger会报错404. 如果整个ingress中有其他服务使用的是/svc则会出现swagger路由到不正确的服务的情况。
这种情况下需要在configurtation-snippet中加上proxy_set_header X-Forwarded-Prefix xxxx的配置。例如代码中我们先动态获取了prefix，将其作为path前缀传入请求。此时在swagger上可以看到请求是/nih/excel。

需要修改application.yml添加配置： server.servlet.forward-headers-strategy改为framework

需要注意的是，在测试过程中，如果修改了congfiguratuion-snippet，需要重启服务以生效。
