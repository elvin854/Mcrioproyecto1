CREACIÓN DE NUEVA CARPETA PARA EL PROYECTO Y VERIFIACION DE VERSIÓN DE VAGRANT.
Carpeta:
 
CREACIÓN DEL VAGRANTFILE:
# vagrant init
 
CREACIÓN DEL VAGRANFILE
# notepad Vagrantile

Creación del vagrantfile:

# -- mode: ruby --
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Máquina 1: web1 (Servidor web con NodeJS y Consul)
  config.vm.define "web1" do |web1|
    web1.vm.box = "ubuntu/jammy64"  # Ubuntu 22.04 LTS
    web1.vm.network "private_network", ip: "192.168.50.11"  # IP fija para web1
    web1.vm.hostname = "web1"
    web1.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y nodejs npm consul
      # Configuración adicional, como la instalación de la app NodeJS
    SHELL
  end

  # Máquina 2: web2 (Otro servidor web con NodeJS y Consul)
  config.vm.define "web2" do |web2|
    web2.vm.box = "ubuntu/jammy64"  # Ubuntu 22.04 LTS
    web2.vm.network "private_network", ip: "192.168.50.12"  # IP fija para web2
    web2.vm.hostname = "web2"
    web2.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y nodejs npm consul
      # Configuración adicional, como la instalación de la app NodeJS
    SHELL
  end

  # Máquina 3: haproxy (Balanceador de carga con HAProxy y Consul)
  config.vm.define "haproxy" do |haproxy|
    haproxy.vm.box = "ubuntu/jammy64"  # Ubuntu 22.04 LTS
    haproxy.vm.network "private_network", ip: "192.168.50.10"  # IP fija para haproxy
    haproxy.vm.hostname = "haproxy"
    haproxy.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y haproxy consul
      # Configuración adicional para configurar HAProxy
    SHELL
  end
end


# vagrant status
 
Ingresamos al ssh haproxy:
Actualizamos y se instalas los archivos necesarios.
 
WEB1:
# vagrant ssh web1
# sudo apt-get update
 
# sudo apt-get install -y nodejs npm
 

WEB2:
# vagrant ssh web2
sudo apt-get update
 
# sudo apt-get install -y nodejs npm
 

CREAMOS UN SERVIDOR WEB BÁSICO
# vim ~/server.js
Agregamos lo siguiente:
const http = require('http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hola, estamos desde web1\n');
});

server.listen(80, () => {
  console.log('Server running on port 80');
});
 
Repetimos el mismo para para el web:1
 

VERFICACION DE PUERTO PARA EL WEB1 Y WEB2
# sudo node ~/server.js
 
 
Evidenciamos el uso del puerto 80.
VM HAPROXY, 
Utilizando usando las IPs  se verifica que se logra acceder  a los servidores.
# vagrant ssh haproxy
 
Configuración de haproxy
# sudo vim /etc/haproxy/haproxy.cfg
 
Agregacion de código:
backend web-backend
  balance roundrobin

  server web1 192.168.50.11:8080 check
  server web2 192.168.50.12:8080 check
 
Guardamos presionando esc y posteriormente: qw

Restaurar el haproxy:
 

USAMOS AUTHBIND PARA PERMITIR QUE NODE.JS
# sudo apt-get install authbind
 
 


 









WEB:1 CONSUL
 

# consul agent -ui -server -bootstrap-expect=1 -bind=192.168.50.11 -client=0.0.0.0 -data-dir=.

WEB2:  CONSUL
# consul agent -bootstrap-expect=1 -bind=192.168.50.12 -client=0.0.0.0 -data-dir=.
 
 
 


