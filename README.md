import threading

recurso_compartido = 0
mutex = threading.Lock()

def hilo_funcion():
    global recurso_compartido
    mutex.acquire() # Adquirir el mutex
    # Sección crítica: acceder al recurso compartido
    recurso_compartido += 1
    print(f"Hilo {threading.current_thread().name}: recurso_compartido = {recurso_compartido}")
    mutex.release() # Liberar el mutex

# Crear y ejecutar hilos
hilos = []
for i in range(5):
    hilo = threading.Thread(target=hilo_funcion, name=f"Hilo-{i}")
    hilos.append(hilo)
    hilo.start()

# Esperar a que todos los hilos terminen
for hilo in hilos:
    hilo.join()

print(f"Valor final del recurso compartido: {recurso_compartido}")
