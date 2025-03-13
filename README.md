import pygame
import random
import time

# Inicializar pygame
pygame.init()

# Configuración de la ventana
ANCHO = 600
ALTO = 400
pantalla = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Simón Dice - Memoria de Números")

# Colores
BLANCO = (255, 255, 255)
NEGRO = (0, 0, 0)
ROJO = (255, 0, 0)
AZUL = (0, 0, 255)
AMARILLO = (255, 255, 0)
VERDE = (0, 255, 0)

# Fuente
fuente = pygame.font.Font(pygame.font.get_default_font(), 30)  # Usamos un tamaño de fuente más pequeño

# Función para mostrar la secuencia con colores y estilo de píxel
def mostrar_secuencia(secuencia, tiempo_espera):
    """Muestra la secuencia de números al jugador en la pantalla"""
    pantalla.fill(NEGRO)  # Fondo negro
    texto = fuente.render("¡Recuerda esta secuencia!", True, AMARILLO)
    pantalla.blit(texto, (150, 50))  # Posiciona el texto en la parte superior

    # Calcular el espacio entre los números para que quepan en la pantalla
    total_ancho = len(secuencia) * 60  # Aproximadamente el ancho total de los números
    espacio_inicial = (ANCHO - total_ancho) // 2  # Centrar la secuencia de números

    # Colores aleatorios para los números en la secuencia
    for i, num in enumerate(secuencia):
        color = random.choice([AZUL, VERDE, ROJO])  # Selecciona un color aleatorio para cada número
        texto_num = fuente.render(str(num), True, color)
        pantalla.blit(texto_num, (espacio_inicial + i * 60, 150))  # Distribuye los números centrados
        pygame.display.update()
        time.sleep(tiempo_espera)  # Pausa de acuerdo con la dificultad

    pygame.display.update()
    time.sleep(1)  # Pausa antes de borrar la pantalla

# Función para obtener la respuesta del jugador
def obtener_respuesta():
    """Obtiene la respuesta del jugador desde el teclado"""
    respuesta = ""
    caja_rect = pygame.Rect(150, 250, 300, 40)  # Definimos el área de la caja de texto
    color_fondo = (30, 30, 30)
    color_texto = (255, 255, 255)
    esperando = True

    while esperando:
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                pygame.quit()
                quit()

            if evento.type == pygame.KEYDOWN:
                if evento.key == pygame.K_RETURN:
                    if respuesta:
                        # Convertir la entrada en una lista de números, permitiendo comas y espacios
                        respuesta_lista = list(map(int, [x.strip() for x in respuesta.replace(",", " ").split()]))
                        esperando = False
                elif evento.key == pygame.K_BACKSPACE:
                    respuesta = respuesta[:-1]  # Eliminar el último carácter
                elif evento.key in range(pygame.K_0, pygame.K_9 + 1) or evento.key == pygame.K_COMMA:
                    respuesta += chr(evento.key)  # Añadir el carácter presionado (números y comas)

        # Rellenar el fondo de la caja de texto
        pantalla.fill(NEGRO)
        texto = fuente.render("Escribe la secuencia de números:", True, AMARILLO)
        pantalla.blit(texto, (100, 50))  # Ubica el texto en la parte superior

        # Mostrar la caja de texto donde se ingresa la respuesta
        pygame.draw.rect(pantalla, color_fondo, caja_rect)  # Dibujar fondo de la caja
        texto_respuesta = fuente.render(respuesta, True, color_texto)
        pantalla.blit(texto_respuesta, (caja_rect.x + 5, caja_rect.y + 5))  # Mostrar lo escrito
        pygame.display.update()

    return respuesta_lista

# Función para determinar la dificultad según la puntuación
def obtener_dificultad(puntuacion):
    """Determina la dificultad del juego según la puntuación"""
    if puntuacion <= 3:
        return 1, 1.2  # Fácil: Números de 1 dígito y tiempo de 1.2 segundos
    elif puntuacion <= 6:
        return 2, 1.0  # Normal: Números de 2 dígitos y tiempo de 1 segundo
    elif puntuacion <= 9:
        return 3, 0.8  # Más difícil: Números de 3 dígitos y tiempo de 0.8 segundos
    else:
        return 4, 0.6  # Difícil: Números de 4 dígitos y tiempo de 0.6 segundos

# Función principal del juego
def jugar():
    """Función principal del juego"""
    secuencia = []
    puntuacion = 0

    while True:
        # Obtener la dificultad según la puntuación
        num_digitos, tiempo_espera = obtener_dificultad(puntuacion)
        
        # Añadir un nuevo número aleatorio a la secuencia (números con la cantidad de dígitos según la dificultad)
        num_min = 10**(num_digitos - 1)  # Genera el número mínimo según la cantidad de dígitos
        num_max = 10**num_digitos - 1  # Genera el número máximo según la cantidad de dígitos
        secuencia.append(random.randint(num_min, num_max))  # Generar números en el rango adecuado
        
        # Mostrar la secuencia con colores
        mostrar_secuencia(secuencia, tiempo_espera)
        
        # Obtener respuesta del jugador
        respuesta = obtener_respuesta()

        # Verificar si la respuesta es correcta
        if respuesta == secuencia:
            puntuacion += 1
            pantalla.fill(NEGRO)
            texto = fuente.render(f"¡Correcto! Tu puntuación es {puntuacion}.", True, AMARILLO)
            pantalla.blit(texto, (100, 150))
            pygame.display.update()
            time.sleep(1)
        else:
            pantalla.fill(NEGRO)
            texto = fuente.render(f"¡Incorrecto! La secuencia era {secuencia}.", True, ROJO)
            pantalla.blit(texto, (100, 150))
            puntuacion_texto = fuente.render(f"Tu puntuación final es {puntuacion}.", True, BLANCO)
            pantalla.blit(puntuacion_texto, (100, 200))
            pygame.display.update()
            time.sleep(2)
            break

        # Tiempo entre rondas para que el jugador se prepare
        time.sleep(1)

# Iniciar el juego
if __name__ == "__main__":
    jugar()
    pygame.quit()
