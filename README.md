# Ansible-AWX-Kurulum
Ansible AWX 17.1.0 sürümünü, Ubuntu 20.04.3 LTS sunucusuna kurulum talimatıdır.

Bu talimat'da bulunan yönergeler, eğitim ve geliştirme ortamları için uygundur.

## Gereksinimler

Ansible Sunucu Donanım gereksinimleri:

|   CPU (Minimum)   |    Ram (Minimum)  |   Hard Disk (Minimum)   |
|-------------------|-------------------|-------------------------|
|   1               |   4 GB            |    20 GB                |

Ansible Sunucu Yazılım gereksinimleri:

- Ansible Version 2.8+
- Docker
- Docker Compose
- python3-pip
- Node
- npm
- git
- pwgen

Detaylı bilgi için: [ Ansible Github](https://github.com/ansible/awx/blob/17.0.1/INSTALL.md)

## Kurulum Adımları:
1. Adım Kurulum yapılacak Suncunun güncelleme işlemi ile başlaycağız.
```
sudo apt update
```
```
sudo apt upgrade -y
```

2. Apt'nin HTTPS üzerinden repository'yi kullanmasına izin vermek için gerekli paketlerin kurulumunu gerçekleştirelim.
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

3. Docker'in resmi GPG anahtarını ekleyelim:
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

4. Docker'in stabil repository kurulumunu gerçekleştirmek için:
```
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

5. Güncelleme dizinini tekrar güncelleyelim:
```
sudo apt update
```

6. Docker Kurulumunu gerçekleştirelim.
```
sudo apt install docker-ce docker-ce-cli containerd.io -y
```

7. Docker'ın çalıştığını teyit edelim.
```
sudo docker run hello-world
```
------------------------------------------------------------------------------------------------------------------------------------------
`docker run` komutunun ekran çıktısı aşağıda ki gibi ise Docker sorunsuz çalışıyor demektir:
------------------------------------------------------------------------------------------------------------------------------------------
![docker_hello_world](https://user-images.githubusercontent.com/66215655/133246335-c61d010c-ab27-449a-8799-364e88e3db16.png)
------------------------------------------------------------------------------------------------------------------------------------------

8. Kullanıcının, Docker komutları için her seferinde sudo kullanımına ihtiyaç duymadan gerçekleştirebilmesi için, mevcut kullanıcıyı Docker grubuna dahil edelim. Mevcut kullanıcı haricinde ki herhangi bir kullanıcı dahil edilmek istenirse `$USER` yerine kullanıcı adı belirtilmelidir.

```
sudo usermod -aG docker $USER
```
