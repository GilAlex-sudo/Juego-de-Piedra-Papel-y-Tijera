# Juego-de-Piedra-Papel-y-Tijera

       <!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piedra, Papel o Tijera</title>
    <!-- Enlace a Tailwind CSS CDN para utilidades básicas, aunque se usará CSS personalizado para el juego -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <style>
        /* Estilos personalizados para el juego */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #2d3748; /* Color de fondo oscuro */
            color: #e2e8f0; /* Color de texto claro */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
            box-sizing: border-box;
        }
        .game-container {
            background-color: #1a202c; /* Fondo del contenedor del juego */
            border-radius: 1.5rem; /* Bordes más redondeados */
            padding: 2.5rem;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.4); /* Sombra más pronunciada */
            text-align: center;
            max-width: 600px;
            width: 100%;
            border: 2px solid #4a5568; /* Borde sutil */
        }

        h1 {
            font-size: 2.5rem;
            font-weight: bold;
            color: #a78bfa; /* Morado vibrante para el título */
            margin-bottom: 1.5rem;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5); /* Sombra de texto */
        }

        p {
            font-size: 1.125rem;
            margin-bottom: 1.5rem;
        }

        .choices-container {
            display: flex;
            justify-content: center;
            gap: 1.5rem; /* Espacio entre los botones de elección */
            margin-bottom: 2rem;
            flex-wrap: wrap; /* Permite que los botones se envuelvan en pantallas pequeñas */
        }

        .choice-button {
            background: linear-gradient(145deg, #4299e1, #63b3ed); /* Degradado azul */
            color: white;
            padding: 1rem 2rem;
            border-radius: 0.75rem;
            font-size: 1.5rem;
            font-weight: bold;
            cursor: pointer;
            border: none;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
            transition: transform 0.2s ease, box-shadow 0.2s ease, background 0.3s ease;
            outline: none;
            min-width: 120px; /* Asegura un tamaño mínimo para los botones */
        }

        .choice-button:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 12px rgba(0, 0, 0, 0.5);
            background: linear-gradient(145deg, #3182ce, #4299e1);
        }

        .choice-button:active {
            transform: translateY(0);
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        #result-message {
            margin-top: 1.5rem;
            font-size: 1.75rem;
            font-weight: bold;
            min-height: 2.5rem; /* Para evitar saltos de diseño */
        }

        .score-container {
            display: flex;
            justify-content: center;
            gap: 2rem;
            margin-top: 1rem;
            font-size: 1.25rem;
            font-weight: bold;
            color: #cbd5e0;
        }

        #resetGameButton {
            background: linear-gradient(145deg, #ef4444, #dc2626); /* Degradado rojo */
            margin-top: 2rem;
            padding: 0.75rem 2rem;
            font-size: 1.25rem;
            border-radius: 0.75rem;
        }
        #resetGameButton:hover {
            background: linear-gradient(145deg, #dc2626, #b91c1c);
        }

        /* Responsive adjustments */
        @media (max-width: 600px) {
            .game-container {
                padding: 1.5rem;
            }
            h1 {
                font-size: 2rem;
            }
            .choices-container {
                flex-direction: column;
                gap: 1rem;
            }
            .choice-button {
                font-size: 1.25rem;
                padding: 0.8rem 1.5rem;
                width: 80%; /* Ocupa más ancho en móvil */
                margin: 0 auto;
            }
            #result-message {
                font-size: 1.25rem;
            }
            .score-container {
                font-size: 1rem;
                gap: 1rem;
            }
            #resetGameButton {
                font-size: 1rem;
                padding: 0.6rem 1.5rem;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Piedra, Papel o Tijera</h1>
        <p>¡Elige tu movimiento!</p>

        <div class="choices-container">
            <button class="choice-button" data-choice="piedra">Piedra</button>
            <button class="choice-button" data-choice="papel">Papel</button>
            <button class="choice-button" data-choice="tijera">Tijera</button>
        </div>

        <p id="result-message">¡Haz tu elección!</p>

        <div class="score-container">
            <span id="player-score">Tú: 0</span>
            <span id="computer-score">Computadora: 0</span>
        </div>

        <button id="resetGameButton">Reiniciar Juego</button>
    </div>

    <script>
        // Variables globales para el juego
        let playerScore = 0;    // Puntuación del jugador
        let computerScore = 0;  // Puntuación de la computadora
        const choices = ['piedra', 'papel', 'tijera']; // Opciones válidas del juego

        // Referencias a elementos del DOM
        const choiceButtons = document.querySelectorAll('.choice-button');
        const resultMessage = document.getElementById('result-message');
        const playerScoreDisplay = document.getElementById('player-score');
        const computerScoreDisplay = document.getElementById('computer-score');
        const resetGameButton = document.getElementById('resetGameButton');

        /**
         * Genera la elección aleatoria de la computadora.
         * @returns {string} La elección de la computadora (piedra, papel o tijera).
         */
        function getComputerChoice() {
            const randomIndex = Math.floor(Math.random() * choices.length);
            return choices[randomIndex];
        }

        /**
         * Determina el ganador de una ronda.
         * @param {string} playerChoice La elección del jugador.
         * @param {string} computerChoice La elección de la computadora.
         * @returns {string} El resultado de la ronda (ganaste, perdiste, empate).
         */
        function determineWinner(playerChoice, computerChoice) {
            if (playerChoice === computerChoice) {
                return 'empate';
            } else if (
                (playerChoice === 'piedra' && computerChoice === 'tijera') ||
                (playerChoice === 'papel' && computerChoice === 'piedra') ||
                (playerChoice === 'tijera' && computerChoice === 'papel')
            ) {
                return 'ganaste';
            } else {
                return 'perdiste';
            }
        }

        /**
         * Actualiza la puntuación y el mensaje de resultado en la interfaz de usuario.
         * @param {string} result El resultado de la ronda (ganaste, perdiste, empate).
         * @param {string} playerChoice La elección del jugador.
         * @param {string} computerChoice La elección de la computadora.
         */
        function updateGame(result, playerChoice, computerChoice) {
            let message = '';
            let messageColor = '';

            if (result === 'ganaste') {
                playerScore++;
                message = `¡Ganaste! ${playerChoice} le gana a ${computerChoice}.`;
                messageColor = '#4ade80'; // Verde
            } else if (result === 'perdiste') {
                computerScore++;
                message = `¡Perdiste! ${computerChoice} le gana a ${playerChoice}.`;
                messageColor = '#f87171'; // Rojo
            } else {
                message = `¡Empate! Ambos eligieron ${playerChoice}.`;
                messageColor = '#fcd34d'; // Amarillo
            }

            resultMessage.textContent = message;
            resultMessage.style.color = messageColor;
            playerScoreDisplay.textContent = `Tú: ${playerScore}`;
            computerScoreDisplay.textContent = `Computadora: ${computerScore}`;
        }

        /**
         * Reinicia las puntuaciones y el estado del juego.
         */
        function resetGame() {
            playerScore = 0;
            computerScore = 0;
            playerScoreDisplay.textContent = 'Tú: 0';
            computerScoreDisplay.textContent = 'Computadora: 0';
            resultMessage.textContent = '¡Haz tu elección!';
            resultMessage.style.color = '#e2e8f0'; // Color de texto por defecto
        }

        // Añadir event listeners a los botones de elección
        choiceButtons.forEach(button => {
            button.addEventListener('click', (event) => {
                const playerChoice = event.target.dataset.choice; // Obtener la elección del jugador
                const computerChoice = getComputerChoice();     // Obtener la elección de la computadora
                const result = determineWinner(playerChoice, computerChoice); // Determinar el ganador
                updateGame(result, playerChoice, computerChoice); // Actualizar la interfaz
            });
        });

        // Añadir event listener al botón de reiniciar
        resetGameButton.addEventListener('click', resetGame);

        // Inicializar el juego al cargar la página (aunque en este caso es solo reiniciar puntuaciones)
        window.onload = resetGame;
    </script>
</body>
</html>
