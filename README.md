# myfirstrobot_description

## Fontes:
https://wiki.ros.org/urdf
https://wiki.ros.org/urdf/XML
https://wiki.ros.org/urdf/Tutorials  


## Dependencias:
ros
rviz
liburdfdom-tools

## tools
check_urdf
urdf_to_graphiz


### Parte 1: Visual

```
    |-- myfirstrobot_description
        |-- config
		    |-- myfirstrobot.rviz
	    |-- launch
		    |-- load_description.launch
	    |-- urdf
		    |-- myfirstrobot_visual.urdf
	    |-- CMakeLists.txt
	    |-- package.xml
        |-- README.md
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot_visual.urdf enable_rviz:=true

### Parte 2: movimentos
```
    |-- myfirstrobot_description
        |-- config
		    |-- myfirstrobot.rviz
	    |-- launch
		    |-- load_description.launch
	    |-- urdf
		    |-- myfirstrobot_visual.urdf
		    |-- myfirstrobot_move.urdf
	    |-- CMakeLists.txt
	    |-- package.xml
        |-- README.md
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot_move.urdf enable_rviz:=true

### Parte 3: organização
```
    |-- myfirstrobot_description
        |-- config
		    |-- myfirstrobot.rviz
	    |-- launch
		    |-- load_description.launch
	    |-- urdf
		    |-- myfirstrobot_visual.urdf
		    |-- myfirstrobot_move.urdf
		    |-- myfirstrobot.urdf.xacro
	    |-- CMakeLists.txt
	    |-- package.xml
        |-- README.md
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot.urdf.xacro enable_rviz:=true enable_jspublisher:=false
