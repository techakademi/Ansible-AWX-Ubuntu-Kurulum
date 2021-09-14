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

9. Docker grubuna eklenen kullanıcının etkin olması için sistemden logout olup tekrar login olalım.

```
logout
```

10. Docker Compose kurulumu için aşağıda ki komutu kopyalayıp yapıştıralım.

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

11. Docker Compose için okuma & yazma yetkisini verelim.

```
sudo chmod +x /usr/local/bin/docker-compose
```

12. Ansible kurulumunu başlatalım.

```
sudo apt install ansible -y
```

13. Her nedense, paketler bir şekilde bozuluyor, apt paketlerini düzeltmek için --fix-missing operatörünü kullanarak düzeltme işlemini gerçekleştirelim.

```
sudo apt update --fix-missing
```

14. Node js & paket yöneticisi olan npm'i yükleme işlemini başlatalım.

```
sudo apt install nodejs npm -y
```


15. Npm'i global olarak yükleyelim.

```
sudo npm install npm --global -y
```

16. Python paket yöneticisi & rastgele paorla üreticisi olan pwgen'i yükleyelim.

```
sudo apt install python3-pip pwgen -y
```

17. Python requests kütüphanesini yükleyelim.

```
sudo pip3 install requests
```

18. Python Docker Compose modülünü yükleyelim.

```
sudo pip3 install docker-compose==1.29.2
```

19. AWX'in github'dan zip'li halini indireceğimiz için, unzip programına ihtiyacımız var, unzip programın yüklemesini gerçekleştirelim.

```
sudo apt install unzip -y
```

20. Wget ile, Ansible AWX'in github'dan 17.1.0 sürümünü indirelim.

```
wget https://github.com/ansible/awx/archive/17.1.0.zip
```

21. İndirdiğimiz zip paketini unzip edelim.

```
unzip 17.1.0.zip
```

22. AWX'in kurulum klasörüne gidelim.

```
cd awx-17.1.0/installer
```


23. AWX'in kurulum klasörüne gidelim.

```
cd awx-17.1.0/installer
```

24. AWX'in kurulumuna başlamadan önce, AWX'in inventory'sinde bulunan erişim bilgilerinin şifrelenmesi için pwgen aracını kullanarak bir Secret_key oluşturacağız. Bu anahtarı oluşturduktan sonra, o nu bir yere kopyalamamız gerekir, daha sonra bu anahtarı inventory belgesi içerisinde kullanacağız.

```
pwgen -N 1 -s 30
```

------------------------------------------------------------------------------------------------------------------------------------------

##### pwgen komutu ile kullandığımız opsionlar:
-N Opsiyonu, oluşturulmasını istediğimiz parola adetinin belirlendiği opsiyon'dur, Örneğin: -N 1, bir adet parola üretecektir, "bir" rakamı yerine ihtiyaç duyulan rakam belirtildiğinde o kadar parola üretecektir.

-s Opsiyonu, oluşturulacak parolanın kaç karakter kullanılarak üretilmesini istediğimizi belirttiğimiz opsiyon'dur.

Daha Fazla Bilgi için: [Pwgen yardım sayfası](https://linux.die.net/man/1/pwgen)

------------------------------------------------------------------------------------------------------------------------------------------

25. AWX envanter belgesi içerisinde admin kullanıcısı, parolası ve bir önce ki adımda oluşturduğumuz anahtarı eklemek için nano veya vi editörlerini kullanarak değişiklikleri uygulayacağız.

```
nano inventory
```

26. AWX envanter belgesinde aşağıda ki satırları bulup işlemlerini gerçekleştirelim:

* Admin kullanıcı satırında bulunan "**admin**" kullanıcı adını bu hali ile kullanabilir veya istediğiniz bir kullanıcı adı belirtebilirsiniz.

* **admin_user=admin**

* Admin kullanıcı parola satırının başında bulunan yorum sembolü'ünü (#) silerek, parola kullanımını etkinleştirip belirledyeceğimiz parolayı kullanabiliriz.

* **admin_password=istediğiniz parola**

* Pwgen ile oluşturduğumuz parolamızı tanımlayalım.

* **secret_key=(pwgen ile oluşturduğunuz anahtar**)

Nano editörü'nde "Ctrl+o" ile kayıt ederiz, "Ctrl+x" ile de editör'den çıkarız.


27. AWX envanter belgesinin düzenlemesinin tamamlanamsından sonra, AWX kurulum playbook'unu çalıştırabiliriz. Bu aşamada, ansible ihtiyaç duyrduğu konteynerleri çalıştırmak için biraz zamana ihtiyaç duyabilir, sonuna kadar beklemenmesi gerekir.

```
ansible-playbook -i inventory install.yml
```

28. Kurulumun tamamlanmasının ardından, `docker run` komutunu kullanarak AWX'in container'leri çalıştırdığını teyit edelim.

```
ansible-playbook -i inventory install.yml
```

###### Kurulum, Başarılı bir şekilde tamamlanması halinde, playbook'un son satırında görevlerin başarılı bir şekilde tamamlandığını görebilriz.
![Kurulum Sonu](https://user-images.githubusercontent.com/66215655/133272355-ddd4fbee-25fc-4db3-a7db-6ae8c0ca6c56.png)


29. AWX docker containerlerini kontrol ettiğimizde de tamamının çalıştığını ayrıca teyit edebiliriz.

```text
[theadmin@AnsibleAWX:/$] docker ps

CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                                   NAMES
adefbc5aea4c   ansible/awx:17.1.0   "/usr/bin/tini -- /u…"   28 minutes ago   Up 28 minutes   8052/tcp                                awx_task
66d3e2afaa09   ansible/awx:17.1.0   "/usr/bin/tini -- /b…"   32 minutes ago   Up 28 minutes   0.0.0.0:80->8052/tcp, :::80->8052/tcp   awx_web
011826f9f780   redis                "docker-entrypoint.s…"   32 minutes ago   Up 28 minutes   6379/tcp                                awx_redis
2c0bc4eeff6b   postgres:12          "docker-entrypoint.s…"   32 minutes ago   Up 28 minutes   5432/tcp                                awx_postgres
```

30. AWX docker containeri awx_web ismi ile, 80 numaralı port'u kullanarak çalışıyor. Yüklemesini gerçekleştirdiğiniz sunucunun ip adresini kullandığınız herhangi bir browserın adres alanına yazarak AWX'in ara yüzüne erişebilirsiniz.
http://x.x.x.x

![AWX_Login](https://user-images.githubusercontent.com/66215655/133275011-2501543e-fe37-4de8-8b44-f1ad1ddb5560.png)

![AWX_Dashboard](https://user-images.githubusercontent.com/66215655/133275044-7729230f-d025-465f-a58a-38aec65751d6.png)
