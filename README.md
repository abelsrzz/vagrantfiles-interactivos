# Script para creación de Vagrantfiles de forma interactiva
<p>Script en bash para crear Vagrantfiles de forma interactiva con preguntas por terminal.</p>
<p>Este tipo de scripts demuestran la gran potencia del lenguaje bash, ya que puede ser combinado con cualquier herramienta de Linux que pueda ser controlado por terminal.
En este caso, en conjunto con Vagrant, podemos crear un script como este, que puede resultar un gran aliado para la creación dinámica de máquinas virtuales.</p>
<h2>Cuenta con:</h2>
<ul>
  <li>Dos boxes por defecto para máquinas Debian o Ubuntu.</li>
  <li>Diversas opciones a configurar en el Vagrantfile.</li>
  <li>Limitaiones a la alta y a la baja de los recursos hardware de la máquina virtual</li>
  <li>Confirmación de eliminación de Vagrantfile antiguo y de inicio de la máquina.</li>
</ul>

<h3>Script:</h3>
```
#!/bin/bash
box=$1
ram=$2
cpus=$3
readyFLag=0
provisionFlag=0

if [ -f "Vagrantfile" ]; then
    echo "Ya existe un Vagrantfile en esta ruta"
    read -p "¿Quieres borrarlo y crear uno nuevo?[s/N]: " rm

    case "$rm" in
        s|S|y|Y)
            rm -rf Vagrantfile
            echo "Borrando Vagrantfile..."
            echo
        ;;
        *) 
            echo "Saliendo"
            exit 1
        ;;
    esac
    

fi

function confAdicionales(){
    echo
    echo "1) Carpeta compartida"
    echo "2) Configuración de red"
    echo "3) Instalación de paquetes"
    echo "4) Abrir máquina con entorno gráfico"
    echo "5) Listo"
    echo "6) Calcelar"
    echo

    read -p "Opción: " conf

    case "$conf" in
        1) 
            echo
            echo "Configurando carpeta compartida..."
            read -p "Ruta absoluta de la carpeta local: " local
            read -p "Ruta absoluta del montaje en la máquina: " montaje

            echo "Se intentará montar $local en $montaje"
            echo "config.vm.synced_folder" "'$local'," "'$montaje'"  >> Vagrantfile
        ;;
        2)
            echo
            echo "1) IP Privada"
            echo "2) IP Pública"
            echo "3) Cancelar"
            echo
            read -p "Opción " n
            case $n in
            1)
                echo
                read -r "Indica la IP privada deseada: " ip
                echo "Se intentarça establecer la IP privada $ip"

                echo "config.vm.network "'private_network'", ip: "'$ip'"" >> Vagrantfile
            ;;
            2)
                echo
                echo "Se intentará establecer una IP pÃºblica por DHCP"
                echo "config.vm.network "'public_network'"" >> Vagrantfile
            ;;
            *)
                echo
                echo "Cancelando..."
                break
            ;;
            esac
        ;;
        3)
            echo "Indica, separados por espacios en blanco, los paquetes que deseas instalar en la máquina: "
            echo "Formato: 'apache2 mysql phpmyadmin'"
            echo
            read -p "Paquetes: " paquetes
            
            nPaquetes=$( echo "$paquetes" | wc -w )
            echo "config.vm.provision 'shell', inline: <<-SHELL">>Vagrantfile
            echo "apt-get update" >>Vagrantfile
            for((i=1;i<=$nPaquetes;i++)); do
                instalarPaquete=$( echo "$paquetes" | cut -d " " -f$i )
                echo "apt-get install -y $instalarPaquete" >>Vagrantfile
                echo "Intentando instalar $instalarPaquete"
            done
            echo "SHELL" >>Vagrantfile          
        ;;
        4)
            provisionFlag=1
            echo "config.vm.provider 'virtualbox' do |vb|">>Vagrantfile
            echo "vb.gui=true">>Vagrantfile
            echo "vb.name='$name'">>Vagrantfile
            echo "vb.memory='$ram'">>Vagrantfile
            echo "vb.cpus='$cpus'">>Vagrantfile
            echo "end">>Vagrantfile


            echo "Intentando arrancar la máquina con entorno gráfico."
        ;;
        5)
            echo "Listo"
            readyFLag=1
        ;;
        6)
            echo "Cancelando"
            readyFLag=1
        ;;
        *)
            echo "Opción incorrecta."
        ;;
           
    esac
    
}

if [ -z "$box" ]
then
    read -p "Introduce el nombre de la box para crear la máquina: " box
fi
read -p "Como quieres que se llame la máquina?: " name
if [ -z "$ram" ];
then
    ram="2048"
elif [ 512 -gt "$ram" ] || [ 4096 -lt "$ram" ];
then
    echo "La memoria RAM debe estar entre 512MB y 4096MB para el correcto funcionamiento de la máquina."
    exit 0
fi

if [ -z "$cpus" ];
then
    cpus="2"
elif [ 1 -gt "$cpus" ] || [ 4 -lt "$cpus" ];
then
    echo "Los cpus deben ser entre 1 y 4 para el correcto funcionamiento de la máquina."
    exit 0
fi

case $box in
    debian)
        box=debian/bookworm64
    ;;
    ubuntu)
        box=ubuntu/noble64
    ;;
esac

echo "Iniciando máquina de vagrant con los siguientes parámetros: "
echo "box: $box"
echo "ram: $ram"
echo "cpus: $cpus"

echo "Vagrant.configure('2') do |config|">>Vagrantfile
echo "config.vm.box='$box'">>Vagrantfile


read -p "¿Quieres realizar alguna configuración extra? [s/N] " option

case "$option" in
y|Y|s|S)
    while [ $readyFLag -ne 1 ]; do
        confAdicionales
    done
;;
esac

if [ $provisionFlag -ne  1 ]; then
    echo "config.vm.provider 'virtualbox' do |vb|">>Vagrantfile
    echo "vb.memory='$ram'">>Vagrantfile
    echo "vb.name='$name'">>Vagrantfile
    echo "vb.cpus='$cpus'">>Vagrantfile
    echo "end">>Vagrantfile
fi
echo "end">>Vagrantfile


read -p "Quieres iniciar la máquina Virtual?[S/n]" inicio
case $inicio in
n|N)
    echo "Máquina no iniciada"
;;
*)
    echo "Iniciando máquina virtual..."
    vagrant up
;;
esac

```
