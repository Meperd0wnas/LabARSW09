## Lab ARSW9

### Daniel Ricardo Ruge Gomez

### Parte 1 - Escalabilidad vertical

#### 1. Creamos la VM

![alt text](images/part1/maquina.png)


#### 2. Nos conectamos a la VM 

![alt text](images/part1/conexion.png)}


#### 3. Instalamos node y git

![alt text](images/part1/node.png)

![alt text](images/part1/git.png)

#### 4. Clonamos un repositorio que solo contenga FIbonacciApp y le instalamos las dependencias

![alt text](images/part1/clonacionenVM.png)


#### 5. ponemos a correr la APP e instalamos forever

![alt text](images/part1/appCorriendo.png)

![alt text](images/part1/forever.png)


#### 6. creamos una Inbound port rule como se nos indico

![alt text](images/part1/verificacionP.png)


#### 7. verificamos que el endPoint funciona (para este punto me toco crear otra VM por que la primera dejo de funcionar)

![alt text](images/part1/endpoint.png)


#### 8. verificamos el consumo

![alt text](images/part1/consumo1.png)

#### 9. modificamos el .JSON

![alt text](images/part1/jsonmod.png)

Resultados del newman

![alt text](images/part1/newman.png)



#### 10. cambiamos el tamaño

![alt text](images/part1/tamaño.png)

#### 11. probamos nuevamente los pasos anteriores


![alt text](images/part1/consumo.png)

![alt text](images/part1/newman1.png)

#### 12. Conclusiones

sando B2ms como escalamiento vertical sí mejora la escalabilidad y evita fallos para cargas moderadas, pero no garantiza cumplimiento absoluto del requisito si el número de usuarios concurrentes sigue aumentando. Para cargas muy altas, sería necesario combinar con escalamiento horizontal (balanceador + múltiples VMs) o mejorar el algoritmo de Fibonacci para reducir el consumo de CPU.

#### 13.

dejamos la VM en su tamaño original