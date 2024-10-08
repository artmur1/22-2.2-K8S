# Домашнее задание к занятию «Хранение в K8s. Часть 2» - `Мурчин Артем`

### Цель задания

В тестовой среде Kubernetes нужно создать PV и продемострировать запись и хранение файлов.

------

### Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным GitHub-репозиторием.

------

### Дополнительные материалы для выполнения задания

1. [Инструкция по установке NFS в MicroK8S](https://microk8s.io/docs/nfs). 
2. [Описание Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). 
3. [Описание динамического провижининга](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/). 
4. [Описание Multitool](https://github.com/wbitt/Network-MultiTool).

------

### Задание 1

**Что нужно сделать**

Создать Deployment приложения, использующего локальный PV, созданный вручную.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Создать PV и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3. Продемонстрировать, что multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории. 
4. Удалить Deployment и PVC. Продемонстрировать, что после этого произошло с PV. Пояснить, почему.
5. Продемонстрировать, что файл сохранился на локальном диске ноды. Удалить PV.  Продемонстрировать что произошло с файлом после удаления PV. Пояснить, почему.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

### Решение 1

Создал Deployment приложения, состоящего из контейнеров busybox и multitool - https://github.com/artmur1/22-2.2-K8S/blob/main/file/deployment.yaml:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/22-2_2-01-deployment.png)

Создал PV и PVC для подключения папки на локальной ноде:

https://github.com/artmur1/22-2.2-K8S/blob/main/file/persistent-volume-claim.yaml

https://github.com/artmur1/22-2.2-K8S/blob/main/file/persistent-volume.yaml

![](https://github.com/artmur1/22-2.2-K8S/blob/main/22-2_2-01-pv.png)

![](https://github.com/artmur1/22-2.2-K8S/blob/main/22-2_2-01-pvc.png)

Поднялись: деплоймент, под с контейнерами, Persistent volume Claim и Persistent Volume:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-01-01.png)

multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-01-02.png)

Удалил Deployment и PVC. PV поменял статус с Bound на Released. До удаления PV был связан с PVC и Deployment, поэтому статус был Bound. А после удаления Deployment и PVC он уже ни с чем не связан, поэтому статус поменялся на  Released:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-01-03.png)

Файл сохранился на локальном диске ноды:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-01-04.png)

Удалил PV, файл также сохранился на локальном диске ноды потому что значение в PV у persistentVolumeReclaimPolicy: Retain - это значит, что после удаления PV ресурсы из внешних
провайдеров автоматически не удаляются.

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-01-05.png)

------

### Задание 2

**Что нужно сделать**

Создать Deployment приложения, которое может хранить файлы на NFS с динамическим созданием PV.

1. Включить и настроить NFS-сервер на MicroK8S.
2. Создать Deployment приложения состоящего из multitool, и подключить к нему PV, созданный автоматически на сервере NFS.
3. Продемонстрировать возможность чтения и записи файла изнутри пода. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

### Решение 2

Включил и настроить NFS-сервер на MicroK8S.

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-02-01.png)

NFS-сервер запущен:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-02-02.png)

Создать Deployment приложения состоящего из multitool - https://github.com/artmur1/22-2.2-K8S/blob/main/file/deployment-multitool.yaml:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/22-2_2-02-deployment-multi.png)

Создал PVC-NFS - https://github.com/artmur1/22-2.2-K8S/blob/main/file/pvc-nfs.yaml:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/22-2_2-02-pvc-nfs.png)

Поднялись: деплоймент, под, Persistent volume Claim и Persistent Volume:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-02-03.png)

Подключился к мультитулу и записал данные в файл, а затем прочитал их внутри пода из PV:

![](https://github.com/artmur1/22-2.2-K8S/blob/main/img/22-2_2-02-04.png)

------

### Правила приёма работы

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
