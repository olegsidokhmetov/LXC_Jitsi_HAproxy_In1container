# Deploy Jitsi

Playbook para desplegar a partir de una plantilla lxc varios servidores Jitsi.
El playbook se divide en 3 tipos de tareas diferentes

* **pre-jitsi** 
* **jitsi**
* **post-jitsi**


Para deploy Jitsi usando playbook `ini.yaml` de la carpeta `/etc/ansible/playbooks`. 
```
---

- hosts: devel-lxd01
  gather_facts: yes
  become: yes
  vars_files:
    - /etc/ansible/roles/1-pre_jitsi/vars/main.yml                #lxc
    - /etc/ansible/roles/1-pre_jitsi/defaults/main.yml            #lxc
    - /etc/ansible/roles/2-Jitsi/defaults/main.yml                #jitsi
    - /etc/ansible/roles/2-Jitsi/vars/main.yml                    #jitsi
    - /etc/ansible/roles/3-post_jitsi/defaults/main.yml           #haproxy
    - /etc/ansible/roles/3-post_jitsi/vars/main.yml               #haproxy

  roles:
  - role: "/etc/ansible/roles/1-pre_jitsi"

- hosts: Jitsi
  gather_facts: yes
  roles:
    - role: "/etc/ansible/roles/2-Jitsi"
    - role: "/etc/ansible/roles/3-post_jitsi"
```


### Pre-jitsi

- Despliege de contenedores en distintos nodos LXD a partir de una plantilla con previamente securizada a nivel de usuarios.

```
Paso:
1-Creating_container.yaml
```

- Configuración de red
```
Paso:
2-Send_netplan_config_to_containers.yaml
```
- Configuración de SSH.
Tarea para recopilar las claves públicas disponibles de múltiples de hosts y copiándolos a un archivo en el servidor principal de Ansible en /root/.ssh/known_hosts
```
Paso:
3-Copy_ssh_to_container.yaml
```

- Si está utilizando un proxy, incluya esta tarea en su rol.
```
3.1-Add_proxy_setiings_in_LXC_container.yaml
```

- Update y upgrade del SO
```
Paso:
4-Update_upgrade_OS.yaml
```

- Instalación de dependencias y software keepalived
```
Paso:
5-Install_dependencias_and_keepalived.yaml
```

- Configuración keepalived
```
6-Configuration_keepalived.yaml
```

### Jitsi 
- Instalación y configuración de jitsi
- Configuración JVB

- Update y upgrade del SO
```
Paso:
1-update_upgrade.yaml
```
- Instalar dependencias
```
Paso:
2-install dependencies.yaml
```
- Servicios de instalación
```
Paso:
3-install_services.yaml
```
- Instalación de gnupg2
```
Paso:
4-install_gnupg2.yaml
```
- Enable apache2 modules
```
Paso:
5-enable_apache2_modules.yaml
```
- Crear el archivo js.consulta.com.conf
```
Paso:
6-create_file_js.quersys.com.conf.yaml
```
- Crear symbolinc links_js.consulta.com.conf
```
Paso:
7-create_symbolic_links_js.quersys.com.conf.yaml
```
- Copia *.cert y *.key de fichero
```
Paso:
8-copy_cert_and_key_file.yaml
```
- Generar pares clave para Shibboleth
```
Paso:
9-generate_key_pair_Shibboleth.yaml
```
- Reiniciar apache
```
Paso:
10-restart_apache.yaml
```
- Nuevo shibboleth de configuración
```
Paso:
11-new_configuration_shibboleth.yaml
```
- Reiniciar shibboleth
```
Paso:
12-restart_shibd_service.yaml
```
- Añadir repositorio de prosody
```
Paso:
13-apt-key_apt-repo_prosody.yaml
```
- Añadir repositorio de jitsi
```
Paso:
14-apt-key_apt-repo_jitsi.yaml
```
- Update y upgrade del SO
```
Paso:
15-update_upgrade.yaml
```
- Añadir datos para el diálogo de confirmación Jitsi
```
Paso:
16-GUI_dialog_install_jitsi.yaml
```
- Installing Jitsi with dialog Confirmations 
```
Paso:
17-install_jitsi-meet.yaml
```
- Configuración de Prosody
```
Paso:
18-configuration_prosody.yaml
```
- Cambiar permisos para localhost.cnf, localhost.crt y localhost.key
```
Paso:
19-change_permission_localhost.cnf_localhost.crt_localhost.key.yaml
```
- Copiar archivo de logotipo a servidor con Jitsi
```
Paso:
20-copy_image_logo.png.yaml
```
- Configuración fichero de Videobridge_sip.comminicator
```
Paso:
21-configuración_Videobridge_sip.comminicator.yaml
```
- Configuración fichero de js.quersys.com-config.js
```
Paso:
22-new_configurationin_js.quersys.com-config.js.yaml
```
- Configuración fichero de file_all.css
```
Paso:
23-configuration_file_all.css.yaml
```
- Reemplazamos de datos file_interface_config.js
```
Paso:
24-replace_data_file_interface_config.js.yaml
```
- Configuración fichero con autenticación shibboleth
```
Paso:
25-autentication_shibboleth.yaml
```
- Agregar un número en orden para el servidor shard-*
```
Paso:
26-next_number_of_shard_script_and_edit_sip-communicator.yaml
```
- Reiniciar servicios
```
Paso:
27-restart_multiple_services.yaml
```

### Post-jitsi 
- Configuración HA
- Configuración de passwords y ficheros entre los dos servidores jitsi para alta disponibilidad.

### Estructura de playbook: (No finalizada) 
```
├── README.md
├── ansible.cfg
├── hosts
├── playbooks
│   ├── start_playbook.yaml
└── roles
    ├── 1-pre_jitsi
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   ├── handlers
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   └── vars
    │       └── main.yml
    ├── 2-Jitsi
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   │   ├── logo
    │   │   └── ssl
    │   ├── handlers
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── tasks
    │   │   └── main.yml
    │   └── vars
    │       └── main.yml
    └── 3-post_jitsi
        ├── defaults
        │   └── main.yml
        ├── files
        ├── handlers
        │   └── main.yml
        ├── meta
        │   └── main.yml
        ├── README.md
        ├── tasks
        │   └── main.yml
        └── templates
```# make jitsi

