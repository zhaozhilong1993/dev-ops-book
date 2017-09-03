# Ansible的强大之处Playbooks

## Playbooks 简介

Playbooks 与 adhoc 相比,是一种完全不同的运用 ansible 的方式,是非常之强大的.

简单来说,playbooks 是一种简单的配置管理系统与多机器部署系统的基础.与现有的其他系统有不同之处,且非常适合于复杂应用的部署.Playbooks 可用于声明配置,更强大的地方在于,在 playbooks 中可以编排有序的执行过程,甚至于做到在多组机器间,来回有序的执行特别指定的步骤.并且可以同步或异步的发起任务.

我们使用 adhoc 时,主要是使用 /usr/bin/ansible 程序执行任务.而使用 playbooks 时,更多是将之放入源码控制之中,用之推送你的配置或是用于确认你的远程系统的配置是否符合配置规范.

在如右的连接中:[ansible-examples repository](https://github.com/ansible/ansible-examples),有一些整套的playbooks,它们阐明了上述的这些技巧.我们建议你在另一个标签页中打开它看看,配合本章节一起看.

## Playbook 语言的示例

Playbooks 的格式是YAML（详见:[_YAML 语法_](http://www.ansible.com.cn/docs/YAMLSyntax.html)）,语法做到最小化,意在避免 playbooks 成为一种编程语言或是脚本,但它也并不是一个配置模型或过程的模型.

playbook 由一个或多个 ‘plays’ 组成.它的内容是一个以 ‘plays’ 为元素的列表.

在 play 之中,一组机器被映射为定义好的角色.在 ansible 中,play 的内容,被称为 tasks,即任务.在基本层次的应用中,一个任务是一个对 ansible 模块的调用,这在前面章节学习过.

‘plays’ 好似音符,playbook 好似由 ‘plays’ 构成的曲谱,通过 playbook,可以编排步骤进行多机器的部署,比如在 webservers 组的所有机器上运行一定的步骤, 然后在 database server 组运行一些步骤,最后回到 webservers 组,再运行一些步骤,诸如此类.

“plays” 算是一个体育方面的类比,你可以通过多个 plays 告诉你的系统做不同的事情,不仅是定义一种特定的状态或模型.你可以在不同时间运行不同的 plays.

对初学者,这里有一个 playbook,其中仅包含一个 play:

```
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum: pkg=httpd state=latest
  - name: write the apache config file
    template: src=/srv/httpd.j2 dest=/etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running
    service: name=httpd state=started
  handlers:
    - name: restart apache
      service: name=httpd state=restarted

```

在下面,我们将分别讲解 playbook 语言的多个特性.

