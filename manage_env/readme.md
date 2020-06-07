# setup user  

```yml
- name: "Add a new user named {{ user_name }}"
    user:
        name: "{{ user_name }}"
        password: "{{ user_password }}"
        comment: "ansible automation service account"
        group: "{{ user_default_group }}"
        # hidden: yes   # macOS only  
        state: present
    # when: ansible not exists  

- name: "Add {{ user_name }} user to the sudoers"
    copy:
        dest: "/etc/sudoers.d/{{ user_name }}"
        content: "{{ user_name }}  ALL=(ALL)  NOPASSWD: ALL"
```

# copy ssh.pub to remote target  



# disable login as root  


```yml
- name: "Disable Password Authentication"
    lineinfile:
        dest=/etc/ssh/sshd_config
        regexp='^PasswordAuthentication'
        line="PasswordAuthentication no"
        state=present
        backup=yes

- name: "Disable Root Login"
    lineinfile:
        dest=/etc/ssh/sshd_config
        regexp='^PermitRootLogin'
        line="PermitRootLogin no"
        state=present
        backup=yes
    notify:
      - restart ssh
       
handlers:
  - name: "restart ssh"
    service:
      name=sshd
      state=restarted
```


---

<table>
    <thead>
        <tr>
            <th colspan=2>Contact:</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3><a href="https://dsmith73.github.io"><img src="https://avatars1.githubusercontent.com/u/44279121?s=60&u=7a933a33b51505f9d6435eeffae1c8156a47dc77&v=4" alt="dsmith73.github.io"></a></td>
            <td><b><a href="https://101101workspace.slack.com/archives/D012ESWSXHQ" alt="dsmith73 on Slack">Slack</a></b></td>
        </tr>
        <tr>
            <td><b><a href="https://discord.gg/RmzVNzx" alt="dsmith73 on Discord">Discord</a></b></td>
        </tr>
        <tr>
            <td><b><a href="skype:dsmith73?chat">Skype</a></b> or <b><a href="https://teams.microsoft.com/l/chat/0/0?users=dsmith73@gmail.com">Teams</b></a></td>
        </tr>
    </tbody>
</table>
