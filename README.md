# Script para creación de Vagrantfiles de forma interactiva
<p>Script en bash para crear Vagrantfiles de forma interactiva con preguntas por terminal.</p>
<p>Este tipo de scripts demuestran la gran potencia del lenguaje bash, ya que puede ser combinado con cualquier herramienta de Linux que pueda ser controlado por terminal.
En este caso, en conjunto con Vagrant, podemos crear un script como este, que puede resultar un gran aliado para la creación dinámica de máquinas virtuales.</p>
<h2>Cuenta con:</h2>
<ul>
  <li>Dos boxes por defecto para máquinas Debian o Ubuntu.</li>
  <li>Diversas opciones a configurar en el Vagrantfile.</li>
  <li>Limitaciones a la alta y a la baja de los recursos hardware de la máquina virtual</li>
  <li>Confirmación de eliminación de Vagrantfile antiguo y de inicio de la máquina.</li>
</ul>
<h2>Forma de uso</h2>

```
./autogenVagrantfiles.sh $box $ram $cpus
```

<p>Siendo <code>$box</code> la Box del <a href="https://app.vagrantup.com/boxes/search">VagrantCloud</a>, $ram la memoria RAM de la máquina (Se establecerán 2GB por defecto.) y $cpus los núcleos que tendrá el procesador de la máquina (Se establecerán 2 núcleos por defecto.)</p>

<h3>Script:</h3>
<a href="https://github.com/abelsrzz/vagrantfiles-interactivos/blob/main/autogenVagrantfile.sh">Ir al script</a>

<h3>Requirements</h3>
<ul>
  <li><a href="https://developer.hashicorp.com/vagrant/downloads">Vagrant</a></li>
  <li><a href="https://www.virtualbox.org/wiki/Downloads">VirtualBox (o similar)</a></li>
</ul>
