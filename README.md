# Домашнее задание к занятию "08.06 Создание собственных modules" - dev-17_ansible-06-my_own_collection-yakovlev_vs

## Подготовка к выполнению
1. Создайте пустой публичный репозиторий в своём любом проекте: `my_own_collection`.
2. Скачайте репозиторий Ansible: git clone `https://github.com/ansible/ansible.git` по любому, удобному вам пути 
3. Зайдите в директорию ansible: `cd ansible`
4. Создайте виртуальное окружение: `python3 -m venv venv`
5. Активируйте виртуальное окружение: `. venv/bin/activate`. Дальнейшие действия производятся только в виртуальном окружении
6. Установите зависимости `pip install -r requirements.txt`
7. Запустить настройку окружения `. hacking/env-setup`
8. Если все шаги прошли успешно - выйти из виртуального окружения `deactivate`
9. Ваше окружение настроено. Чтобы запустить его, нужно находиться в директории `ansible` и выполнить конструкцию `. venv/bin/activate && . hacking/env-setup`

#### Выполнено

## Основная часть

Ваша цель — написать собственный module, который вы можете использовать в своей role через playbook. Всё это должно быть собрано в виде collection и отправлено в ваш репозиторий.
#### Решение

1. ### В виртуальном окружении создайте новый `my_own_module.py` файл

- `Создан`

2. ### Наполнить его содержимым:

<details><summary></summary>

```python
#!/usr/bin/python

# Copyright: (c) 2018, Terry Jones <terry.jones@example.org>
# GNU General Public License v3.0+ (see COPYING or https://www.gnu.org/licenses/gpl-3.0.txt)
from __future__ import (absolute_import, division, print_function)
__metaclass__ = type

DOCUMENTATION = r'''
---
module: my_test

short_description: This is my test module

# If this is part of a collection, you need to use semantic versioning,
# i.e. the version is of the form "2.5.0" and not "2.4".
version_added: "1.0.0"

description: This is my longer description explaining my test module.

options:
    name:
        description: This is the message to send to the test module.
        required: true
        type: str
    new:
        description:
            - Control to demo if the result of this module is changed or not.
            - Parameter description can be a list as well.
        required: false
        type: bool
# Specify this value according to your collection
# in format of namespace.collection.doc_fragment_name
extends_documentation_fragment:
    - my_namespace.my_collection.my_doc_fragment_name

author:
    - Your Name (@yourGitHubHandle)
'''

EXAMPLES = r'''
# Pass in a message
- name: Test with a message
  my_namespace.my_collection.my_test:
    name: hello world

# pass in a message and have changed true
- name: Test with a message and changed output
  my_namespace.my_collection.my_test:
    name: hello world
    new: true

# fail the module
- name: Test failure of the module
  my_namespace.my_collection.my_test:
    name: fail me
'''

RETURN = r'''
# These are examples of possible return values, and in general should use other names for return values.
original_message:
    description: The original name param that was passed in.
    type: str
    returned: always
    sample: 'hello world'
message:
    description: The output message that the test module generates.
    type: str
    returned: always
    sample: 'goodbye'
'''

from ansible.module_utils.basic import AnsibleModule


def run_module():
    # define available arguments/parameters a user can pass to the module
    module_args = dict(
        name=dict(type='str', required=True),
        new=dict(type='bool', required=False, default=False)
    )

    # seed the result dict in the object
    # we primarily care about changed and state
    # changed is if this module effectively modified the target
    # state will include any data that you want your module to pass back
    # for consumption, for example, in a subsequent task
    result = dict(
        changed=False,
        original_message='',
        message=''
    )

    # the AnsibleModule object will be our abstraction working with Ansible
    # this includes instantiation, a couple of common attr would be the
    # args/params passed to the execution, as well as if the module
    # supports check mode
    module = AnsibleModule(
        argument_spec=module_args,
        supports_check_mode=True
    )

    # if the user is working with this module in only check mode we do not
    # want to make any changes to the environment, just return the current
    # state with no modifications
    if module.check_mode:
        module.exit_json(**result)

    # manipulate or modify the state as needed (this is going to be the
    # part where your module will do what it needs to do)
    result['original_message'] = module.params['name']
    result['message'] = 'goodbye'

    # use whatever logic you need to determine whether or not this module
    # made any modifications to your target
    if module.params['new']:
        result['changed'] = True

    # during the execution of the module, if there is an exception or a
    # conditional state that effectively causes a failure, run
    # AnsibleModule.fail_json() to pass in the message and the result
    if module.params['name'] == 'fail me':
        module.fail_json(msg='You requested this to fail', **result)

    # in the event of a successful module execution, you will want to
    # simple AnsibleModule.exit_json(), passing the key/value results
    module.exit_json(**result)


def main():
    run_module()


if __name__ == '__main__':
    main()
```

Или возьмите данное наполнение из [статьи](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html#creating-a-module).
</details>

- # `сделано`

3. ### Заполните файл в соответствии с требованиями ansible так, чтобы он выполнял основную задачу: module должен создавать текстовый файл на удалённом хосте по пути, определённом в параметре `path`, с содержимым, определённым в параметре `content`.

- Обновленный файл: [my_own_module.py](./ansible_venv/my_own_module.py)

4. ### Проверьте module на исполняемость локально.

- Добавил файл hello.json:
```bash
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cat hello.json
{
    "ANSIBLE_MODULE_ARGS": {
        "path": "/tmp/test_01.txt",
        "content": "Hello, netology!"
    }

```
- # `Проверяем`  

```bash
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# python -m ansible.modules.my_own_module hello.json

{"changed": true, "original_message": "Hello, netology!", "message": "File was created!", "invocation": {"module_args": {"path": "/tmp/test_01.txt", "content": "Hello, netology!"}}}
```  

- # `Содержимое файла:`  

```bash  
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cat /tmp/test_01.txt
Hello, netology!
```  
  
- # `Изменим аргументы и попробуем повторно записать данные в файл:`  

```bash  
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cat hello.json
 {
    "ANSIBLE_MODULE_ARGS": {
        "path": "/tmp/test_01.txt",
        "content": "Hello, netology!\nChecking multiline text"
    }
}
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# python -m ansible.modules.my_own_module hello.json

{"changed": false, "original_message": "Hello, netology!\nChecking multiline text", "message": "File already exist", "invocation": {"module_args": {"path": "/tmp/test_01.txt", "content": "Hello, netology!\nChecking multiline text"}}}
```
- # `Файл уже существует`
- # `оздаем новый с этим же контентом и проверяем содержимое`  

```bash
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cat hello.json
{
    "ANSIBLE_MODULE_ARGS": {
        "path": "/tmp/test_02.txt",
        "content": "Hello, netology!\nChecking multiline text"
    }
}(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# python -m ansible.modules.my_own_module hello.json

{"changed": true, "original_message": "Hello, netology!\nChecking multiline text", "message": "File was created!", "invocation": {"module_args": {"path": "/tmp/test_02.txt", "content": "Hello, netology!\nChecking multiline text"}}}
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cat /tmp/test_02.txt
Hello, netology!
Checking multiline text
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible#
```

5. ### Напишите single task playbook и используйте module в нём.

```bash
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# ansible-playbook single_task_playbook.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] **********************************************************************************************************************************************************************

TASK [Execute module] **************************************************************************************************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
- файл
```bash
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cat /tmp/test_03.txt 
Test module in task
Hello, netology! 
```

6. ### Проверьте через playbook на идемпотентность.
```bash

(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# ansible-playbook single_task_playbook.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my_own_module] **********************************************************************************************************************************************************************

TASK [Execute module] **************************************************************************************************************************************************************************
ok: [localhost]

PLAY RECAP *************************************************************************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

7. ### Выйдите из виртуального окружения.
- # `Выполнено`

```bash
(venv) ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# deactivate
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# 
```

8. ### Инициализируйте новую collection: `ansible-galaxy collection init my_own_namespace.yandex_cloud_elk`


```bash
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# ansible-galaxy collection init my_own_namespace.yandex_cloud_elk
- Collection my_own_namespace.yandex_cloud_elk was created successfully
```

9. ### В эту collection перенесите свой module в соответствующую директорию.

```bash
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# ls -l my_own_namespace/yandex_cloud_elk/plugins/modules/
total 4
-rw-r--r-- 1 root root 3837 Nov 18 20:55 my_own_module.py
```

10. ### Single task playbook преобразуйте в single task role и перенесите в collection. У role должны быть default всех параметров module  

- # `defaults/main.yml:`

```yaml
---
test_path: "/tmp/test_remote_01.txt"
test_content: "Test module in task\nHello, netology!\n"
```
- tasks/main.yml:
```yaml
---
- name: Execute module
  my_own_module:
    path: "{{ test_path }}"
    content: "{{ test_content }}"
```
- # `inventory/hosts.yml:`
```yaml
---
my_test:
  hosts:
    ubuntu-ansible:
      ansible_host: 109.191.54.132
      ansible_connection: ssh
```

11. ### Создайте playbook для использования этой role.

- # `site.yml:`

```yaml
---
- name: Test my_own_module in yandex.cloud
  hosts:
    - ubuntu-ansible
  roles:
    - my_role
```  

- # `запуск playbook  `


```bash  
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible/my_own_namespace/yandex_cloud_elk# ansible-playbook -i inventory/hosts.yml site.yml 

PLAY [Test my_own_module in yandex.cloud] ******************************************************************************************************************************************************

TASK [my_role : Execute module] ****************************************************************************************************************************************************************
changed: [ubuntu-ansible]

PLAY RECAP *************************************************************************************************************************************************************************************
ubuntu-ansible             : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

12. ### Заполните всю документацию по collection, выложите в свой репозиторий, поставьте тег `1.0.0` на этот коммит.

- # `сделано`

13. ### Создайте .tar.gz этой collection: `ansible-galaxy collection build` в корневой директории collection.  

```bash  
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible/my_own_namespace/yandex_cloud_elk# ansible-galaxy collection build
Created collection for my_own_namespace.yandex_cloud_elk at /root/ansible/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
```  

14. ### Создайте ещё одну директорию любого наименования, перенесите туда single task playbook и архив c collection.  

```bash  
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cp my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz my_own_namespace_new/
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cp my_own_namespace/yandex_cloud_elk/site.yml my_own_namespace_new/ 
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible# cd my_own_namespace_new/
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible/my_own_namespace_new# ls -l
total 12
-rw-r--r-- 1 root root 6091 Nov 19 21:08 my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
-rw-r--r-- 1 root root   88 Nov 19 21:12 site.yml
```  

15. ### Установите collection из локального архива: `ansible-galaxy collection install <archivename>.tar.gz`

```bash
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible/my_own_namespace_new# ansible-galaxy collection install my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz -p collection
Starting galaxy collection install process
[WARNING]: The specified collections path '/root/ansible/my_own_namespace_new/collection' is not part of the configured Ansible
collections paths '/root/.ansible/collections:/usr/share/ansible/collections'. The installed collection won't be picked up in an Ansible
run.
Process install dependency map
Starting collection install process
Installing 'my_own_namespace.yandex_cloud_elk:1.0.0' to '/root/ansible/my_own_namespace_new/collection/ansible_collections/my_own_namespace/yandex_cloud_elk'
my_own_namespace.yandex_cloud_elk:1.0.0 was installed successfully
```

16. ### Запустите playbook, убедитесь, что он работает.

```bash
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible/my_own_namespace_new/collection/ansible_collections/my_own_namespace/yandex_cloud_elk# ansible-playbook -i inventory/hosts.yml site.yml

PLAY [Test my_own_module in yandex.cloud] ******************************************************************************************************************************************************

TASK [my_role : Execute module] ****************************************************************************************************************************************************************
changed: [ubuntu-ansible]

PLAY RECAP *************************************************************************************************************************************************************************************
ubuntu-ansible             : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu@compute-vm-4-4-30-hdd-1744535447294:~/ansible/my_own_namespace_new/collection/ansible_collections/my_own_namespace/yandex_cloud_elk# ansible-playbook -i inventory/hosts.yml site.yml

PLAY [Test my_own_module in yandex.cloud] ******************************************************************************************************************************************************

TASK [my_role : Execute module] ****************************************************************************************************************************************************************
changed: [ubuntu-ansible]

PLAY RECAP *************************************************************************************************************************************************************************************
ubuntu-ansible             : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

17. ### В ответ необходимо прислать ссылку на репозиторий с collection

[my_collection](./my_own_namespace/yandex_cloud_elk/)