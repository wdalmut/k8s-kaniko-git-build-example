# Kaniko build example (from Git)

Just a simple example to build a ndoejs application using K8S with Kaniko and
Git repositories

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  initContainers:
  - name: git-clone
    image: alpine
    command: [ 'sh', '-c' ]
    args: [
      'apk add -Uuv git openssh-client && git clone https://github.com/corley/api-skel.git /workspace'
    ]
    volumeMounts:
      - name: kaniko-secret
        mountPath: /root
      - name: dockerfile-storage
        mountPath: /workspace
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    args: ["--dockerfile=Dockerfile_prod",
            "--destination=wdalmut/api-skel-kaniko"] # replace with your dockerhub account
    volumeMounts:
      - name: kaniko-secret
        mountPath: /root
      - name: dockerfile-storage
        mountPath: /workspace
  restartPolicy: Never
  volumes:
    - name: kaniko-secret
      secret:
        secretName: regcred
        items:
          - key: .dockerconfigjson
            path: .docker/config.json
    - name: dockerfile-storage
      emptyDir: {}
```
