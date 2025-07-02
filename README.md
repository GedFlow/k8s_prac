# 쿠버네티스 미니 프로젝트
### Skills
- Docker version 28.3.0, build 38b7060
  - 도커 이미지 베이스 : python version = 3.9.21-slim
- kubernetes 1.31
    - minikube 사용함
    - kustomization 사용

## ToDo
```
[완]- web자체 이미지 제작
[완]- ingress 리소스 생성
[완]- 자체 제작한 이미지로 deploment 생성 (flask와 DB)
[완]- ingress - flask(deploy) - DB(deploy) 구성\
[완]- ingress TLS 터미네이션 설정
[완]- flask 디플로이먼트 HPA 구성 추가
[완]- flask <-> DB 연동
[완]- flask configmap으로 구성 설정
[완]- DB configmap으로 구성 설정
[완]- DB PV/PVC 구성 추가
[완]- Helm 배포
- DB statefulSet으로 이중화 (시간 남으면 할 것)
```

## 사용설명서
```bash
# helm chart 실행
$ helm install <별칭> ptj-miniproject/flaskApp

# helm chart 제거
$ helm uninstall <별칭>

# kustomize 사용시
kubectl apply -k ./project
```
위 명령어를 사용하여 즉시 Ingress - web(flask) - DB 세팅을 구성할 수 있다.
이 경우 DB가 hostPath를 사용하는 볼륨을 사용한다.

```yaml
# db-deploy.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        ports:
        - containerPort: 3306
        envFrom:
        - secretRef:
            name: mysql-secret
        - configMapRef:
            name: mysql-configmap
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc-hp # hostPath 사용 설정
          #claimName: mysql-pvc-nfs # nfs 사용 설정
```
위 코드는 **db-deploy.yml** 파일 내용이다.
맨 아래 주석처리 된 부분을 설명대로 mysql-pvc-hp를 mysql-pvc-nfs 로 교체하여 nfs 볼륨을 사용할 수 있다. 

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /srv/nfs-volume
    server: 192.168.100.25 # 이 부분 ip를 nfs가 가동중인 서버의 ip로 바꿔줄 것
```
위 코드는 **pv-nfs.yml** 파일 내용이다.
맨 아래 주석 설명대로 ip주소를 nfs-server가 가동중인 서버의 ip주소로 바꿔줘야 한다!

> nfs 볼륨을 사용하려면 먼저 control plane에 nfs-server를 설치 및 실행하고, /srv/nfs-volume 경로를 생성해 주어야 한다.
