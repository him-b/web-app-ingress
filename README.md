simple nginx-app-deploy using jenkins, docker,kubernetes(microk8s)(installing all of these on ubuntu ec2 machine)

install necessary tools such as jenkins,docker and microk8s and add usergroups to jenkins
1)sudo snap alias microk8s.kubectl kubectl(alias for microk8s.kubectl)
install ingress controller inside microk8s using command:
2)microk8s enable ingress

add microk8 as usergroup to jenkins:
3)sudo usermod -a -G microk8s jenkins
4)sudo chown -R jenkins ~/.kube
5)newgrp microk8s

access application using http://<ec2public ip>:<nodeport>

<img width="1882" height="861" alt="image" src="https://github.com/user-attachments/assets/ba819f18-3dfc-4ef1-8db9-8fd42ec51c20" />
<img width="1891" height="841" alt="image" src="https://github.com/user-attachments/assets/4d9339ce-f96d-4c31-a917-92623b18ac11" />
<img width="1912" height="882" alt="image" src="https://github.com/user-attachments/assets/7789d0dd-983a-4a8c-b1fc-9366bae8df30" />
