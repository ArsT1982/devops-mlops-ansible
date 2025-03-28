# devops-mlops-ansible
Домашняя работа к занятию “Ansible”.

Базовое использование Ansible для управления хостами, установки пакетов, создания пользователей и копирования файлов.

---

## Инвентори файл (inventory.ini)

```ini
[netology-ml]
localhost ansible_user=ansible ansible_password=Ansible_123 ansible_connection=local ansible_become=yes ansible_become_method=sudo
```

---

## Плейбук (`homework.yaml`)

```yaml
---
- name: Netology Ansible Homework
  hosts: netology-ml
  become: true
  vars:
    packages:
      - net-tools
      - git
      - tree
      - htop
      - mc
      - vim
    users:
      - devops_1
      - test_1

  tasks:
    - name: Проверка доступности хостов
      ping:

    - name: Обновление кэша пакетов
      apt:
        update_cache: yes

    - name: Установка необходимых пакетов
      apt:
        name: "{{ packages }}"
        state: present

    - name: Копирование файла test.txt
      copy:
        src: files/test.txt
        dest: /tmp/test.txt
        mode: '0644'

    - name: Создание пользователей и домашних директорий
      block:
        - name: Создание пользователя "{{ item }}"
          user:
            name: "{{ item }}"
            state: present
            shell: /bin/bash
            create_home: yes
          loop: "{{ users }}"
```

---

## Запуск плейбука и логирование результата

```bash
ansible-playbook -i inventory.ini homework.yaml | tee ansible.log
```

---

## Результаты выполнения

```bash
PLAY [Netology Ansible Homework] *******************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [localhost]

TASK [Проверка доступности хостов] *****************************************************************************
ok: [localhost]

TASK [Обновление кэша пакетов] *********************************************************************************
ok: [localhost]

TASK [Установка необходимых пакетов] ***************************************************************************
changed: [localhost]

TASK [Копирование файла test.txt] ******************************************************************************
changed: [localhost]

TASK [Создание пользователей и домашних директорий] ************************************************************
changed: [localhost] => (item=devops_1)
changed: [localhost] => (item=test_1)

PLAY RECAP *****************************************************************************************************
localhost                  : ok=6    changed=3    unreachable=0    failed=0
```

---

## Проверка созданных пользователей

1. Проверка пользователей в системе:

```bash
cat /etc/passwd 
```

2. Проверка их домашних директорий:

```bash
ls /home
```

Оба пользователя `devops_1` и `test_1` - есть в выводе.


