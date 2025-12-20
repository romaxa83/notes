#server #devops #IaC #ansible

- MANUAL - https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html
- INSTALL - https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-18-04-ru

#### 🔹 Команды

```bash
# запустить 
ansible-playbook site.yml

# проверить хосты
ansible all -m ping -i host.yml
#флаг -i указывает с какогго файла подгружать хосты
```

> `ansible` при запуске команд в таске ,сначала проверяет запущена ли эта команда уже, и если нет то запускает иначе пропускает

---