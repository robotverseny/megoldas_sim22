# Megoldás
🤖 Autonóm robot verseny megoldás template

# Telepítés

A minél szélesebb használhatóság érdeklében leírjuk a Windows alapú (WSL) és natív Ubuntu telepítés lépéseit is. A használt szoftververziók:
- Ubuntu 18.04
- ROS Melodic

Az első lépés esetében tehát választhattok az `1/A.` vagy az `1/B.` opció közül:

## `1/A.` WSL alapú telepítés
A Windows Subsystem for Linux egy kompatibilitási réteg Linux-alapú elemek natív futtatásához Windows 10, vagy Windows 11 alapú rendszereken. Akkor válasszátok a WSL használatát, ha nem szeretnétek natív Ubuntu 18.04-et telepíteni a számítógépeitekre.

- Rendszergazdaként futtatva nyissatok egy PowerShell ablakot.
- Másoljátok be az alábbi parancsot. Ezzel engedélyezitek a WSL használatát.
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```
- Indítsátok újra a számítógépet az ```Y``` betű beírásával.
- Nyissátok meg a Microsoft Store-t, és keressetek rá az Ubuntu 18.04-re. Telepítsétek.
- A könnyebb kezelhetőség érdekében érdemes telepíteni a Windows Terminal programot is. Szintén a Microsoft Store-ban keressetek rá a Windows Terminal-ra, és telepítsétek.
- Indítsátok el a Windows Terminal programot, és a Ctrl+, (Control és vessző) billentyűkombinációval nyissátok meg a beállításokat. A Default Profile beállítási sor legördülő listájából válasszátok az Ubuntu 18.04-et. 
- Indítsátok újra a Windows Terminal-t. Az első induláskor adjatok meg tetszőleges felhasználónevet és jelszót. 
- A megoldás kidolgozásához a VS Code szerkesztőt javasoljuk. Telepítsétek innen: https://code.visualstudio.com/download
- Végül telepítsétek a VS Code Remote Development kiegészítőjét, hogy WSL használatával is elérhető legyen: https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack

## `1/B.` Natív Ubuntu alapú telepítés
Ubuntu 18.04
- VS code szerkesztő: https://code.visualstudio.com/download

## `2.` ROS telepítése

A következő lépések végrehajtásával az ROS Melodic változatát fogjuk telepíteni. 

### `2.1.` Forrás megadása

Adjuk hozzá a packages.ros.org helyet a telepítési források listájához:

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

### `2.2.` Kulcs megadása

Állítsuk be az ROS Melodic telepítéséhez szükséges kulcsot:

```
sudo apt install curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

### `2.3.` Telepítés

Frissítsük a Debian csomagokat:

```
sudo apt update
```
A következő parancs a tényleges telepítést fogja indítani:
```
sudo apt install ros-melodic-desktop-full
``` 
A telepítést követően tegyük a basrc-be az ROS környezeti változóját:

```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
### Megjegyzés

További információk a telepítéssel kapcsolatban elérhetőek itt: http://wiki.ros.org/melodic/Installation/Ubuntu

## `3.` szimulátor és a példamegoldás telepítése

A szükésges csomagok így telepíthetőek:

```
sudo apt-get -y install ros-melodic-ros-control ros-melodic-gazebo-ros-control ros-melodic-ros-controllers ros-melodic-navigation qt4-default ros-melodic-ackermann-msgs ros-melodic-serial ros-melodic-teb-local-planner* ros-melodic-tf-conversions zip unzip ros-melodic-jsk-rviz-plugins python-catkin-tools
```

Készítsünk egy külön workspace-t ('sim_ws'), hogy később könnyen törölhessük, ha már nem kell.

```
cd ~
mkdir -p sim_ws/src
cd ~/sim_ws/src
git clone https://github.com/robotverseny/racecar_gazebo
git clone https://github.com/robotverseny/megoldas
cd ~/sim_ws
catkin init
catkin build
```

Adjuk meg bashrc-ben a szimulátorhoz szükséges modellek elérési útvonalát.

```
echo "export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:~/sim_ws/src/racecar_gazebo/f1tenth/virtual/dependencies/racecar_gazebo/models" >> ~/.bashrc
source ~/.bashrc

```

Hogy ne kelljen minden terminalban megadnunk a workspace-t, tegyük azt is a bashrc-be. Ha ezt nem szerenénk, elég mindig kiadni a `source ~/sim_ws/devel/setup.bash` parancsot.

```
echo "source ~/sim_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
Később a `bashrc`-ből törölhető ez a sor, nyissuk meg vs code-ból: `code ~/.bashrc`.

### TODO

# Fejlesztés

**Fontos**, hogy a középiskola neve és azonosítója ki legyen töltve, így a `/kozepiskola` topic-ban a tényleges középisola név és azonosító szerepeljen. Ezt a legkönnyebben a `pid_error.py` fájl elején lévő változók átírásával lehet elérni (ékezetek nélkül).

Tehát a következő állapot alaphelyzet, hibás beküldést jelent:

``` bash
$ rostopic echo -n1 /kozepiskola
data: "Ismeretlen kozepiskola(A00)"
```

Link: https://github.com/robotverseny/megoldas/blob/main/src/pid_error.py#L15-L16

Terminal1:
```
roscore
```
Terminal2:
```
roslaunch racecar_gazebo racecar.launch
```
*Tipp:* `rosservice call /gazebo/reset_simulation "{}"` paranccsal alaphelyzetbe állítható a szimulátor, de a `megoldas1.launch`-ban lévő:
``` xml
<node pkg="rosservice" type="rosservice" name="rosservice" args="call /gazebo/reset_simulation"/>
```
ugyanezt teszi.

Terminal3:
```
roslaunch megoldas megoldas1.launch
```


# Beküldés

```
cd sim_ws/src
zip -r megoldas.zip megoldas
```
### TODO
