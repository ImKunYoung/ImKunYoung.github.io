---
layout: post
title: 쿠버네티스 서비스 타입
---

쿠버네티스는 컨테이너 오케스트레이션 도구로서, 애플리케이션의 효율적인 배포를 지원하는데, 이를 위해 다양한 서비스 타입을 제공합니다. 이러한 서비스 타입들은 각기 다른 특성을 가지며, 애플리케이션의 요구사항과 환경에 맞게 선택하여 사용할 수 있습니다.

<br>
<br>




## 1. ClusterIP



ClusterIP 서비스 타입은 쿠버네티스 클러스터 내에서만 접근 가능한 내부 서비스를 생성합니다. 이 서비스 타입은 서비스에 고정된 IP 주소를 할당하며, 클러스터의 다른 파드들이 이 IP 주소를 사용하여 서비스에 접근할 수 있습니다. 외부 네트워크에서는 이 서비스에 접근할 수 없으며, 클러스터 내에서만 사용되는 경우에 적합합니다.

![](https://velog.velcdn.com/images/gun_123/post/9d99fc43-e231-4656-88bf-7a589985ecad/image.webp)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-internal-service
spec:
  selector:
    app: my-app
  type: ClusterIP
  ports:
    - port: 80 # 서비스의 포트
      targetPort: 8080 # 포드의 포트
```

![](https://velog.velcdn.com/images/gun_123/post/d4ceaf0b-1c7b-4e76-be39-0276598d246e/image.webp)



<br>

## 2. NodePort



NodePort 서비스 타입은 클러스터 내의 모든 노드들의 특정 포트에 서비스를 노출합니다. 이를 통해 외부 클라이언트가 쿠버네티스 클러스터에 접근할 수 있습니다. 이 서비스 타입은 클러스터 외부에서 접근해야 하는 경우에 유용합니다. NodePort 서비스를 생성하면 노드의 IP 주소와 지정된 포트로 서비스에 접근할 수 있습니다.


![](https://velog.velcdn.com/images/gun_123/post/9e9808cc-4934-4c7b-9180-83651b81e26f/image.webp)


```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  selector:
    app: my-app
  type: NodePort
  ports:
    - port: 80 # 서비스의 포트
      targetPort: 8080 # 포드의 포트
      nodePort: 30000 # 최종적으로 서비스되는 포트
```

![](https://velog.velcdn.com/images/gun_123/post/2bc23508-a589-4ff2-948f-e746c917e552/image.webp)

<br>

## 3. LoadBalancer

LoadBalancer 서비스 타입은 클라우드 제공 업체의 로드 밸런서를 사용하여 서비스를 외부에 노출합니다. 이 서비스 타입은 외부로 서비스를 노출하면서 로드 밸런서를 이용하여 트래픽을 여러 백엔드 파드로 분산시킬 수 있습니다.

![](https://velog.velcdn.com/images/gun_123/post/ee6514a5-b036-4577-be20-4ccf811a21c5/image.webp)



```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  selector:
    app: my-app
  type: LoadBalancer
  ports:
    - port: 80 # 최종적으로 서비스할 포트
      targetPort: 8080 # 파드의 포트
```

<br>

## 4. ExternalName

ExternalName 서비스 타입은 외부 DNS 이름을 쿠버네티스 내부에서 사용할 수 있도록 해줍니다. 이를 통해 외부 서비스에 대한 CNAME 레코드를 생성하고, 이를 쿠버네티스 클러스터 내부에서 사용할 수 있습니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-externalname-service
spec:
  type: ExternalName
  externalName: my-external-service.example.com
```


<br>

## 5. Headless

Headless 서비스 타입은 ClusterIP와 다르게 가상 IP 주소를 할당하지 않으며, 각 파드에 직접 접근할 수 있도록 해줍니다. 이를 통해 DNS 조회를 통해 파드의 직접적인 IP 주소를 얻을 수 있으며, 파드 간의 직접 통신이 필요한 경우에 사용됩니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```


<br>




![](https://velog.velcdn.com/images/gun_123/post/116683bd-b87c-4b94-8da8-07351a52b66a/image.png)


<br>






## 6. Ingress

Ingress는 클러스터 외부에서 내부로 접근하는 트래픽을 관리하는 API 오브젝트입니다. Ingress는 클라이언트의 요청을 서비스로 전달하는 규칙을 정의하며, 일반적으로 HTTP와 HTTPS를 지원하는 웹 애플리케이션의 노출에 사용됩니다.

Ingress는 주로 LoadBalancer 서비스 타입과 함께 사용되며, 외부에서 접근 가능한 URL과 해당 URL로 요청이 들어왔을 때 서비스로 어떻게 연결할지를 정의합니다. 이를 통해 단일 외부 IP 주소를 사용하여 여러 서비스를 노출하고, 도메인 기반 라우팅 및 경로 기반 라우팅 등을 구현할 수 있습니다.

![](https://velog.velcdn.com/images/gun_123/post/995d3590-1f72-4e6b-8fc7-dd503622cb6a/image.webp)


```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /app1
            pathType: Prefix
            backend:
              service:
                name: app1-service
                port:
                  number: 80
          - path: /app2
            pathType: Prefix
            backend:
              service:
                name: app2-service
                port:
                  number: 80
```

위 예시에서는 Ingress를 통해 `example.com/app1`과 `example.com/app2` 경로로 들어오는 요청을 각각 `app1-service`와 `app2-service`로 연결합니다. 

Ingress 컨트롤러는 실제로 Ingress를 해석하고 트래픽을 서비스로 전달하는 역할을 수행합니다. 대표적인 Ingress 컨트롤러로는 Nginx Ingress Controller, Traefik, HAProxy Ingress 등이 있습니다.



<br>

## 요약


| 기능               | ClusterIP                                                    | NodePort                                                      | LoadBalancer                                                 | Ingress                                                      |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 노출 방식          | 내부 클러스터의 내부 IP 주소를 사용하여 서비스 노출             | 외부 클라이언트에 서비스 노출                                 | 외부 클라이언트에 서비스 노출                                 | 외부 클라이언트에 서비스 노출                                 |
| 클러스터 접근      | 클러스터 내부에서만 접근 가능한 서비스                        | NodePort 서비스로, 각 클러스터 노드의 특정 포트를 열어 서비스 노출 | 로드 밸런서를 사용하여 서비스 노출, 클라우드 인프라에 따라 로드 밸런서 자동 생성 | LoadBalancer 서비스와 함께 사용하여 외부 IP 주소 또는 도메인과 서비스 연결 |
| 접근 방식          | 내부 클라이언트는 안정적인 내부 IP 주소로 요청 전송           | 내부 클러스터 IP-포트 및 모든 노드의 전용 포트를 통해 접근 가능 | 클라이언트는 로드 밸런서의 IP 주소를 통해 서비스에 접근       | 도메인 기반 라우팅, 경로 기반 라우팅 등을 사용하여 트래픽 전달 |
| YAML 구성          | type: ClusterIP                                              | type: NodePort                                               | type: LoadBalancer                                           | `apiVersion: networking.k8s.io/v1`<br>`kind: Ingress`         |
| 포트 범위          | 클러스터 내부의 모든 IP 주소                                | 30000 - 32767                                                | 클러스터 내부의 모든 IP 주소                                 | -                                                            |
| 사용 사례          | 내부 통신을 위한 서비스                                      | 테스트를 위한 공개 또는 비공개 접근 또는 제한된 기간 동안 접근을 제공하는 데 유용 | 외부 통신을 위해 널리 사용함                                 | 웹 애플리케이션의 외부 노출 및 라우팅                      |


<br>

## 결론

쿠버네티스는 다양한 서비스 타입을 제공하여 애플리케이션의 효율적인 배포와 접근을 가능하게 합니다. ClusterIP, NodePort, LoadBalancer, ExternalName, 그리고 Headless 서비스 타입을 적절히 조합하여 애플리케이션을 개발하고 운영할 수 있습니다. 각 서비스 타입의 특성과 장단점을 잘 이해하여, 애플리케이션 요구사항에 최적화된 서비스 타입을 선택하는 것이 중요합니다.

<br>
<br>




> **Reference: ** 
>
>[**1. Kubernetes NodePort vs LoadBalancer vs Ingress? When should I use what?**](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)
>
> [2. kubernetes-service-publishing](https://theithollow.com/2019/02/05/kubernetes-service-publishing/)
>
> [3. whats-the-difference-between-clusterip-nodeport-and-loadbalancer-service-types](https://stackoverflow.com/questions/41509439/whats-the-difference-between-clusterip-nodeport-and-loadbalancer-service-types)

