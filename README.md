import threading
import time
import random

# Variable global compartida
shared_counter = 0

# Mutex para proteger el acceso a shared_counter
counter_lock = threading.Lock()

# Semáforo para limitar el número de hilos que pueden ejecutar una sección crítica simultáneamente (máximo 3)
concurrency_semaphore = threading.Semaphore(3)

# Barrera para sincronizar la ejecución de todos los hilos (en este ejemplo, se crean 10 hilos)
num_threads = 10
sync_barrier = threading.Barrier(num_threads)

def thread_task(thread_id):
    global shared_counter
    print(f"Thread {thread_id} iniciado.")

    # Simula trabajo previo a la sección crítica
    time.sleep(random.uniform(0.1, 0.5))
    
    # Sección controlada por semáforo
    with concurrency_semaphore:
        print(f"Thread {thread_id} adquirió el semáforo.")
        # Sección crítica protegida por mutex
        with counter_lock:
            print(f"Thread {thread_id} ingresando a la sección crítica (mutex).")
            # Simula una operación que toma tiempo (por ejemplo, actualización de un contador)
            temp = shared_counter
            time.sleep(random.uniform(0.1, 0.3))
            shared_counter = temp + 1
            print(f"Thread {thread_id} actualizó shared_counter a {shared_counter}.")
        print(f"Thread {thread_id} liberó el semáforo.")
    
    # Sincronización: cada hilo espera en la barrera hasta que todos hayan llegado
    print(f"Thread {thread_id} esperando en la barrera.")
    sync_barrier.wait()
    print(f"Thread {thread_id} superó la barrera.")

def main():
    threads = []
    # Creación y lanzamiento de hilos
    for i in range(num_threads):
        t = threading.Thread(target=thread_task, args=(i + 1,))
        threads.append(t)
        t.start()
    
    # Espera a que todos los hilos finalicen
    for t in threads:
        t.join()

    print("Todos los hilos han finalizado.")
    print(f"Valor final de shared_counter: {shared_counter}")

if __name__ == "__main__":
    main()
