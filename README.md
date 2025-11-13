# Lab ARSW9

### Autor: Daniel Ricardo Ruge Gómez

---

## Parte 1 - Escalabilidad vertical

### 1. Creación de la máquina virtual

![alt text](images/part1/maquina.png)

### 2. Conexión a la VM

![alt text](images/part1/conexion.png)

### 3. Instalación de Node.js y Git

![alt text](images/part1/node.png)  
![alt text](images/part1/git.png)

### 4. Clonación del repositorio y configuración

Clonamos un repositorio que solo contiene **FibonacciApp** e instalamos sus dependencias.

![alt text](images/part1/clonacionenVM.png)

### 5. Ejecución de la aplicación e instalación de Forever

![alt text](images/part1/appCorriendo.png)  
![alt text](images/part1/forever.png)

### 6. Creación de la regla de puerto de entrada

![alt text](images/part1/verificacionP.png)

### 7. Verificación del endpoint

Para este punto se creó una nueva VM, ya que la inicial dejó de funcionar correctamente.

![alt text](images/part1/endpoint.png)

### 8. Verificación del consumo

![alt text](images/part1/consumo1.png)

### 9. Modificación del archivo JSON

![alt text](images/part1/jsonmod.png)

**Resultados del Newman**

![alt text](images/part1/newman.png)

### 10. Cambio de tamaño de la VM

![alt text](images/part1/tamaño.png)

### 11. Nuevas pruebas de consumo

![alt text](images/part1/consumo.png)  
![alt text](images/part1/newman1.png)

### 12. Conclusiones

Usando **B2ms** como escalamiento vertical, se mejora la capacidad de procesamiento y se evita el fallo bajo cargas moderadas.  
Sin embargo, no garantiza el cumplimiento del requisito si el número de usuarios concurrentes aumenta.  
Para cargas muy altas, se recomienda combinar con **escalamiento horizontal** (balanceador + múltiples VMs) o mejorar el algoritmo de Fibonacci.

### 13. Retorno al tamaño original de la VM

La VM se dejó nuevamente en su tamaño inicial.

---

## Preguntas - Parte 1

### ¿Cuántos y cuáles recursos crea Azure junto con la VM?

Azure crea automáticamente varios recursos al desplegar una VM:

| Recurso | Función |
|----------|----------|
| Virtual Machine (VM) | Ejecuta la aplicación y procesos del sistema. |
| Network Interface (NIC) | Permite la conectividad de red entre la VM y otros recursos. |
| Network Security Group (NSG) | Controla el tráfico entrante y saliente hacia la VM. |
| Public IP Address | Permite acceder a la VM desde Internet. |
| Virtual Network (VNet) | Aísla la VM y permite conectividad privada entre recursos de Azure. |
| OS Disk / Storage Account | Almacena el sistema operativo y datos persistentes. |
| Availability Zone/Set | Proporciona redundancia y alta disponibilidad. |

Total aproximado: **6–7 recursos** dependiendo de la configuración.

---

### ¿Por qué se cae la app al cerrar SSH y por qué se necesita una inbound port rule?

- **Cierre de SSH:** al ejecutar `node FibonacciApp.js` directamente, el proceso corre solo en la sesión activa de la terminal. Al cerrar SSH, la sesión termina y el proceso también.  
  Usar **Forever** permite que la app continúe ejecutándose en segundo plano.

- **Inbound port rule:** Azure bloquea el tráfico entrante por defecto. Para aceptar peticiones HTTP en el puerto 3000, se requiere crear una regla de entrada que permita ese tráfico.

---

### Tabla de tiempos de respuesta

| n (Fibonacci) | Tiempo total (s) |
|---------------|------------------|
| 1,000,000 | 10.14 |
| 1,010,000 | 10.23 |
| 1,020,000 | 10.64 |
| 1,030,000 | 11.14 |
| 1,040,000 | 11.11 |
| 1,050,000 | 11.29 |
| 1,060,000 | 11.53 |
| 1,070,000 | 11.64 |
| 1,080,000 | 11.98 |
| 1,090,000 | 12.42 |

**Interpretación:**  
El tiempo aumenta porque el algoritmo de Fibonacci usado es recursivo sin optimización, generando cálculos redundantes y un alto consumo de CPU.

---

### Consumo de CPU

Durante la prueba, el consumo de CPU llegó cerca del **100%**, indicando que la función bloquea el hilo principal y satura la VM fácilmente.

---

### Resumen de ejecución de Postman (Newman)

- Tiempos de ejecución: entre **10–12 segundos**.  
- Fallos: algunos bajo alta concurrencia.  
- Conclusión: la VM **B1ls** no soporta múltiples peticiones simultáneas sin degradarse.

---

### Diferencia entre B2ms y B1ls

| Característica | B1ls | B2ms |
|----------------|------|------|
| vCPU | 1 | 2 |
| RAM | 0.5 GB | 8 GB |
| Uso recomendado | Cargas ligeras | Cargas medianas |
| Ventaja adicional | Bajo costo | Más capacidad y paralelismo |

**Conclusión:**  
El escalamiento vertical mejora la capacidad de respuesta, pero no soluciona la ineficiencia del código. El aumento de tamaño solo retrasa la saturación.

---

### ¿Hubo mejora en consumo de CPU y tiempos de respuesta?

- **CPU:** se redujo la saturación.  
- **Tiempos:** similares, ya que el cuello de botella está en el algoritmo.  
- **Ejecuciones paralelas:** la B2ms soportó más carga sin fallos.

---

### Conclusión final parte 1

El **escalamiento vertical** mejora el rendimiento hasta cierto punto, pero no es una solución definitiva.  
Para mayor eficiencia y tolerancia a fallos, es recomendable aplicar **escalamiento horizontal** con un balanceador de carga.

---

## Parte 2 - Escalabilidad horizontal

### 1. Creación del balanceador de carga

![alt text](images/part2/balanceador.png)

### 2. Creación del Backend Pool

![alt text](images/part2/pool.png)

### 3. Creación del Health Probe

![alt text](images/part2/probe.png)

### 4. Creación de la Load Balancing Rule

![alt text](images/part2/rule.png)

### 5. Creación de la Virtual Network

![alt text](images/part2/RV.png)

### 6. Creación de las máquinas virtuales

![alt text](images/part2/maquinas.png)

Luego de clonar el repositorio y ejecutar la aplicación, se verificó que el endpoint funciona correctamente.  
El resultado muestra una infraestructura más **eficiente y tolerante a fallos**.

---

## Parte 2 - Preguntas

### 1. ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?

Azure ofrece dos tipos principales:

- **Public Load Balancer:** maneja tráfico externo desde Internet hacia la red virtual.  
- **Internal Load Balancer:** maneja tráfico interno dentro de la misma red o entre redes emparejadas.

La diferencia está en el **alcance del tráfico**: el público es externo, el interno es privado.

---

### 2. ¿Qué es SKU, qué tipos hay y en qué se diferencian?

**SKU (Stock Keeping Unit)** define la versión del recurso y sus capacidades.

- **Basic:** sin soporte para zonas, menos seguro y para pruebas.  
- **Standard:** con soporte para zonas, métricas avanzadas y uso en producción.

---

### 3. ¿Por qué el balanceador de carga necesita una IP pública?

Para recibir y distribuir tráfico desde Internet hacia las máquinas virtuales.  
Sin IP pública, solo puede manejar tráfico interno (balanceador interno).

---

### 4. ¿Cuál es el propósito del Backend Pool?

Agrupa las máquinas que recibirán tráfico balanceado, distribuyendo las solicitudes entre ellas para mejorar disponibilidad y rendimiento.

---

### 5. ¿Cuál es el propósito del Health Probe?

Monitorea la salud de las instancias del backend pool.  
Si una VM falla, el balanceador deja de enviarle tráfico hasta que se recupere.

---

### 6. ¿Cuál es el propósito de la Load Balancing Rule?

Define cómo se distribuye el tráfico entrante entre las máquinas del backend (puertos, protocolos, sonda, etc.).

**Tipos de sesión persistente:**
- **None:** cada solicitud puede ir a cualquier instancia.  
- **Client IP:** el mismo cliente siempre va a la misma VM.  
- **Client IP and Protocol:** mantiene sesión por IP y protocolo.

Esto es útil para servicios con estado, aunque puede reducir la escalabilidad.

---

### 7. ¿Qué es una Virtual Network y una Subnet?  
¿Para qué sirven los address space y address range?

- **Virtual Network (VNet):** red privada en Azure.  
- **Subnet:** división lógica dentro de la VNet.  
- **Address Space:** rango total de IPs disponibles.  
- **Address Range:** rangos asignados a cada subred.

Permiten organización, control y segmentación del tráfico.

---

### 8. ¿Qué son las Availability Zones y por qué seleccionamos 3 diferentes zonas?  
¿Qué significa que una IP sea zone-redundant?

Las **Availability Zones** son centros de datos separados físicamente dentro de una región.  
Usar 3 zonas distintas asegura alta disponibilidad ante fallos.

Una **IP zone-redundant** funciona en todas las zonas, garantizando continuidad incluso si una zona falla.

---

### 9. ¿Cuál es el propósito del Network Security Group (NSG)?

Actúa como un firewall interno que controla el tráfico de red entrante y saliente.  
Permite definir reglas por protocolo, IP y puerto para mejorar la seguridad.

---

## Conclusión General

- El **escalamiento vertical** (B2ms) mejora el rendimiento inicial, pero no soluciona la saturación bajo alta concurrencia.  
- El **escalamiento horizontal** con balanceador de carga distribuye mejor las peticiones y ofrece alta disponibilidad.  
- Combinando ambos enfoques se logra una solución **más robusta, eficiente y tolerante a fallos.**

---
