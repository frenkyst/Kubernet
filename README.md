# Kubernet


- Sebelum melakukan instalasi kubernetes diwajibkan terlebih dahulu untuk NON-Aktifkan firewall dan SWAP
  
      ufw disable

  ![image](https://user-images.githubusercontent.com/40049149/191898965-a7b88d56-b36a-4383-98e2-14db8791b733.png)

      swapoff -a; sed -i '/swap/d' /etc/fstab

  ![image](https://user-images.githubusercontent.com/40049149/191899042-eca78148-13bc-428d-9e0b-c24c7af3971f.png)

- kemudian kita perlu juga update kernel

      cat >>/etc/sysctl.d/kubernetes.conf<<EOF
      net.bridge.bridge-nf-call-ip6tables = 1
      net.bridge.bridge-nf-call-iptables = 1
      EOF

- Kemudian Restart system

      sysctl --system

  ![image](https://user-images.githubusercontent.com/40049149/191899398-90c27601-bac6-4318-99a9-3f4c83b2f836.png)

- Konfigurasi docker daemon

      cat <<EOF | sudo tee /etc/docker/daemon.json
      {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
      "max-size": "100m"
      },
      "storage-driver": "overlay2"
      }
      EOF

  ![image](https://user-images.githubusercontent.com/40049149/191899505-a9204a60-12bd-4a65-9c04-19f546cfbbac.png)

- Restart Docker

      sudo systemctl enable docker
      sudo systemctl daemon-reload
      sudo systemctl restart docker

  ![image](https://user-images.githubusercontent.com/40049149/191899624-51b248c6-4e85-4005-b40e-0ef0d2bcbf82.png)

- Instalasi Kubernetes kubelet kubeadm kubectl

      sudo apt-get update && sudo apt-get install -y apt-transport-https curl
      curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
      cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
      deb https://apt.kubernetes.io/ kubernetes-xenial main
      EOF
      sudo apt-get update

  ![image](https://user-images.githubusercontent.com/40049149/191902202-fec46f5f-5d4e-44d1-9cf5-644a6cfb9ebb.png)

      sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni

  ![image](https://user-images.githubusercontent.com/40049149/191902265-73a2056d-ec38-4cc5-891c-ea619827dff4.png)

      sudo systemctl daemon-reload
      sudo systemctl restart kubelet

  ![image](https://user-images.githubusercontent.com/40049149/191902323-f3494584-ee4e-46b8-8220-ae1e97d6c5c5.png)

- Konfigurasi kubeadm

      sudo sudo kubeadm init

  ![Screenshot from 2022-09-23 13-49-26](https://user-images.githubusercontent.com/40049149/191906234-a84ffc85-2bfe-4f62-afaf-f60acde7fa04.png)

- Konfigurasi kubernetes agar dapat menjalankan perintah

      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config

  ![image](https://user-images.githubusercontent.com/40049149/191907048-e770dd30-10cb-4bb8-8549-4b47e94fa77e.png)

      kubectl apply -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml

  ![image](https://user-images.githubusercontent.com/40049149/191907502-bc3bd45e-8b37-44bb-a44b-c3ccdb08148d.png)

- Mengecek semua pods gunakan perintah

      kubectl get pods --all-namespaces

  ![image](https://user-images.githubusercontent.com/40049149/191907592-3fa1450c-a5e2-4441-b417-95a891fc65fc.png)

- Lalu lakukan join cluster dengan perintah berikut

      kubeadm token create --print-join-command

  ![image](https://user-images.githubusercontent.com/40049149/191907771-1db4a24b-8f28-4660-a353-00174388cd4d.png)

Kemudian copy semua hasil outputnya

NOTE : INSTALL DAHULU KUBERNATES DENGAN VERSI YANG SAMA !!!

Copy pada kedua worker

![image](https://user-images.githubusercontent.com/40049149/191915924-44551d7b-a6b8-4e50-af0f-60f13cb3306f.png)


[WORKER 1]

[WORKER 2]

Kemudian cek koneksi nodes menggunakan perintah

    kubectl get nodes

![image](https://user-images.githubusercontent.com/40049149/191915964-cfcdfd01-be35-498a-a6a6-d7c17b2c5f4c.png)

## Deploy Simple App

- Kemudian saya akan mendeploy nginx

      kubectl create deploy nginx --image nginx

  ![image](https://user-images.githubusercontent.com/40049149/191916419-a3daab60-4391-450a-83c7-8fa070653fa4.png)

- Buildnya gunakan perintah

      kubectl expose deploy nginx --port 80 --type NodePort
      
  ![image](https://user-images.githubusercontent.com/40049149/191916473-bf452833-4f01-441d-9e6f-cc6a03c46a96.png)

- Cek service gunakan perintah

      kubectl get svc
      
  ![image](https://user-images.githubusercontent.com/40049149/191916552-485eca55-00a3-4889-be4e-e1ab6643b92f.png)
      
- Untuk melihat informasi lengkap pada pod gunakan

      kubectl describe pod nama pod

  ![image](https://user-images.githubusercontent.com/40049149/191916783-eff5b539-7f61-45c9-ad96-7c896f2b0a1f.png)

- Nginx berjalan pada worker 2 






