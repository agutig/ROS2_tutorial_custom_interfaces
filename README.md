# ROS2 Tutorial: Custom interfaces (Incompleto) [SPANISH/ESPAÑOL]

* Autor: Agutig (Álvaro "Guti" Gutiérrez García)
* Ultima revisión: Mayo 2024
* Version de ROS 2: Humble.
* OS: Linux Ubuntu.

## Resumen

Las interfaces en ROS 2 representan los mensajes a través de los cuales se facilita la comunicación. Aunque estas interfaces pueden diseñarse específicamente para un paquete determinado, es recomendable generarlas en un paquete separado e independiente. Esto se debe a que, conceptualmente, otros nodos podrían recibir y entender los mensajes personalizados (interfaces) incluso si los paquetes de nodos publicador/suscriptor fueron diseñados para funcionar únicamente en parejas. 

## Tutorial

### 1. Crear el paquete de forma independiente. Da igual si lo generas con la instruccion de c++ o de python.

```Terminal
    c++:               ros2 pkg create --build-type ament_cmake <package_name>
```

```Terminal
    python:          ros2 pkg create --build-type ament_python <package_name>
```


### 2. Borrar todo en el paquete excepto los archivos CMakeLists.txt, package.xml

<img src="https://github.com/agutig/media_repository/blob/main/ros2_custom_interfaces_tutorial/empty_package_0.png" width="400" height="250">

### 3. Crear una carpeta msg donde guardaremos las estructuras de los .msg . La estructura se debe ver asi:

<img src="https://github.com/agutig/media_repository/blob/main/ros2_custom_interfaces_tutorial/empty_package_1.png" width="500" height="300">

### 4. En la carpeta msg crear nuestro primer archivo “.msg” que definirá la estructura de los mensajes. NOTA: La primera letra del archivo debe ser en mayúsculas

<img src="https://github.com/agutig/media_repository/blob/main/ros2_custom_interfaces_tutorial/empty_package_2.png" width="500" height="300">

### 5.Dentro de esta archivo, añadiremos los elementos que necesitaremos en nuestras interfaces, similar a un json. La estructura que debemos seguir es

```
tipo nombre_variable
```

Ejemplo para un msg de tipo imagen

```
# Custom.msg

uint16 original_width
uint16 original_height

uint8[] vector_r #R matrix flattened into a vector
uint8[] vector_g #G matrix flattened into a vector
uint8[] vector_b #B matrix flattened into a vector
```




### 6. Configurar el CMakeLists.txt . Principalmente hay que añadir las siguientes lineas: 

```txt
find_package(rosidl_default_generators REQUIRED)  # Needed to add to use interfaces
find_package(builtin_interfaces REQUIRED)         # Needed to add to use interfaces

# Add this lines too
rosidl_generate_interfaces(${PROJECT_NAME}
 "msg/Nombre_interfaz.msg"                                  # Adding the created interfaces
 DEPENDENCIES builtin_interfaces
)

ament_export_dependencies(rosidl_default_runtime)  # This line is also necesary
```

### 7. Configurar el package.xml. Simplemente añade las siguientes lineas

```xml
<buildtool_depend>rosidl_default_generators</buildtool_depend>  <!-- Add this-->
<exec_depend>rosidl_default_runtime</exec_depend>               <!-- Add this-->
<member_of_group>rosidl_interface_packages</member_of_group>    <!-- Add this-->
```


### 8. Finalmente solo queda utilizar la interfaz en otros proyectos.

#### 8.1. C++
Para añadirlos:

```cpp
#include "nombre_paquete_interfaz/msg/nombre_interfaz.hpp"  #OJO en minusculas
```

#Si se pone como archivo .hpp aunque no hayas definido ningun archivo .hpp como tal

para declarar un objeto msg

```cpp
nombre_paquete_interfaz::msg::Nombre_interfaz   msg;  #AHORA EN MAYUSCULAS
```

Y finalmente podemos rellenar sus atributos como:

```cpp
msg.width = 1920;   
msg.height = 1080;   
```


#### 8.2. Python
Para utilizar tus interfaces personalizadas en un proyecto Python:

Importa la interfaz desde tu paquete:

```python
from nombre_paquete_interfaz.msg import NombreInterfaz
```
y para declarar un objeto msg

```python
msg = NombreInterfaz()
```

Y finalmente podemos rellenar sus atributos como:

```python
msg.width = 1920
msg.height = 1080
```
