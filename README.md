
# **Slurm**. Практикум по Ansible s052081

### **Задание:**

1. Установите операционную систему на виртуальную машину (рекомендуется Ubuntu 20.04 или выше, CentOS).  
   **Примечание:** если вы используете другую операционную систему, укажите в заметках, как скачать соответствующий образ для Vagrant.

2. Настройте приложение [Semaphore](https://semaphoreui.com/) и организуйте его автоматический запуск:
   - Запуск можно реализовать с помощью **docker container** или через **systemd service** (на выбор).  
   - Если вы выберете запуск через systemd, воспользуйтесь готовыми файлами service, которые находятся в репозитории (ссылка на репозиторий указана в практических рекомендациях).
   - Убедитесь, что приложение автоматически запускается после перезагрузки виртуальной машины.

3. Переведите приложение на использование базы данных PostgreSQL вместо MySQL:
   - Установите и настройте PostgreSQL с помощью роли из **Ansible Galaxy**.
   - Создайте базу данных, настройте пользователя и задайте необходимые права для работы приложения.
   - Подробные рекомендации по миграции с MySQL на PostgreSQL можно найти в разделе Практических рекомендаций.
---
## **Semaphore UI**

Развертывание Semaphore реализовано двумя плейбуками:
1. Установка и настройка PostgreSQL и базы данных
2. Развертывание сервиса Semaphore и Nginx
### Prerequisites
1. В качестве рабочей ОС используется **"Rocky Linux 9.4 (Blue Onyx)"** под управлением **Vagrant** в среде **VirtualBox**. Использовался вполне дружелюбный образ **[generic/rocky9](https://portal.cloud.hashicorp.com/vagrant/discover/generic/rocky9)**
2. В процессе работы используются дополнительные роли и коллекции:
```yaml
roles:
- src: geerlingguy.postgresql

collections:
- name: community.postgresql
- name: community.crypto
```

Для включения из в свое окружение выполняем:

```bash
ansible-galaxy install -r requirements.yml
ansible-galaxy collection install -r requirements.yml
```
### Переменные
Все настройки вынесены в групповые переменные
### Реквизиты доступа
Перед установкой следует сгенерировать необходимые пароли доступа к БД и GUI Semaphore. Файл `pg_pass` должен быть исполняемым.

```bash
ansible-vault encrypt_string --vault-id postgres@pg_pass
```

Будет сгенерирован зашифрованный пароль, который следует вставить в переменную в `playbook.yml`
### Запуск
Развертывание приложения выполняется поочередным запуском двух плейбуков:

```bash
ansible-playbook playbooks/playbook.yml  --vault-id postgres@pg_pass
ansible-playbook playbooks/semaphore.yml --vault-id postgres@pg_pass
```
