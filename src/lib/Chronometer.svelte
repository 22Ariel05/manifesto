<script>
    import { time, running } from '$lib/stores.js';

    let interval;        // Identifiant de setInterval()
    let startTime = 0;   // Pour enregistrer l'heure de démarrage
    let lastUpdate = 0;  // Pour actualiser 'time' en continu

    // Lance le chrono
    function start() {
        if (!$running) {
            // On considère que l'utilisateur a déjà un "time" existant ou non
            // => s'il a déjà tourné avant, on repart de $time
            startTime = Date.now() - $time;
            running.set(true);

            interval = setInterval(() => {
                lastUpdate = Date.now() - startTime;
                time.set(lastUpdate);
                document.title = formatTime(lastUpdate); // Mettre à jour le titre du doc
            }, 10);
        }
    }

    // Met le chrono en pause
    function pause() {
        running.set(false);
        clearInterval(interval);
        document.title = "Chronomètre";
    }

    // Remet le chrono à zéro
    function reset() {
        pause();
        time.set(0);
        document.title = "Chronomètre";
    }

    // Formatte le temps (ms) en mm:ss:xxx
    function formatTime(ms) {
        const minutes = Math.floor(ms / 60000);
        const seconds = Math.floor((ms % 60000) / 1000);
        const milliseconds = ms % 1000;
        return `${String(minutes).padStart(2, '0')}:
            ${String(seconds).padStart(2, '0')}:
            ${String(milliseconds).padStart(3, '0')}`;
    }
</script>

<h1>Chronomètre</h1>
<p>{formatTime($time)}</p>

<button on:click={start} disabled={$running}>Démarrer</button>
<button on:click={pause} disabled={!$running}>Pause</button>
<button on:click={reset}>Reset</button>
