<script>
    // Importiert die 'wireframeJSONToSVG'-Funktion aus der 'roadmap-renderer'-Bibliothek
    import {wireframeJSONToSVG} from 'roadmap-renderer';

    // Deklariert die Variable 'svgElement' die wird and das HTML Element gebunden
    let svgElement;
    let isModalOpen = false;
    let message;

    const onClick = (event) => {
        const clickable = event.currentTarget;
        const token = clickable.getAttribute('data-group-id');
        message = token;
        isModalOpen = true;
    }
    // Definiert die Funktion 'jsonToSvg', die eine JSON-Datei abruft, sie in SVG umwandelt
    // und das resultierende SVG-Element an das HTML-Div mit der ID 'roadmap' anhängt.
    const jsonToSvg = (jsonUrl) => {
        // Die 'fetch'-Methode wird verwendet, um die JSON-Datei von der angegebenen URL abzurufen
        return fetch(jsonUrl)
            // Die Antwort der 'fetch'-Methode wird in JSON-Format umgewandelt
            .then((res) => {
                return res.json();
            })
            // Die 'wireframeJSONToSVG'-Funktion wird auf die JSON-Daten angewendet, um ein
            // SVG-Element zu erstellen
            .then((json) => {
                return wireframeJSONToSVG(json, {
                    fontURL: '/fonts/balsamiq.woff2',
                });
            })
            // Das resultierende SVG-Element wird an die 'svgElement'-Variable angehängt
            .then((svg) => {
                const clickables = svg.getElementsByClassName('clickable-group');
                for (let i = 0; i < clickables.length; i++) {
                    const clickable = clickables[i];
                    clickable.addEventListener('click', onClick);
                }
                svgElement.appendChild(svg);
            })
            // Wenn ein Fehler auftritt, wird eine Fehlermeldung in der Konsole ausgegeben
            .catch((error) => {
                console.log("Failed to load balsamiq JSON");
            });
    };

    // Ruft die Funktion 'jsonToSvg' auf, um die JSON-Datei '/roadmaps/chatgpt.json'
    // abzurufen und in SVG umzuwandeln
    jsonToSvg('/roadmaps/chatgpt.json');

</script>

<div id="roadmap" bind:this={svgElement}></div>

{#if isModalOpen}
    <div class="modal-overlay">
        <div class="modal">
            <span class="close" on:click={() => isModalOpen = false}>×</span>
            <h1>Learning Journey</h1>
            <h2>{message.split(':')[2].replaceAll('-', ' ')}</h2>
            <p>{message}</p>
        </div>
    </div>
{/if}

<style>
    /* Legt die Breite und Höhe des SVG-Elements fest */
    :global(div#roadmap ) {
        width: 800px;
        height: auto;
    }

    /* Ändert den Mauszeiger, wenn der Benutzer über ein klickbares Element im SVG-Element hovert */
    :global(div#roadmap .clickable-group) {
        cursor: pointer;
    }

    /* Ändert die Füllfarbe des SVG-Elements, wenn der Benutzer über ein bestimmtes Element hovert */
    :global(div#roadmap .clickable-group:hover > [fill="rgb(255,229,153)"]) {
        fill: rgb(205, 179, 103);
    }

    /* Ändert die Füllfarbe des SVG-Elements, wenn der Benutzer über ein bestimmtes Element hovert */
    :global(div#roadmap .clickable-group:hover > [fill="rgb(255,255,0)"]) {
        fill: rgb(235, 235, 0);
    }

    .modal {
        position: fixed;
        top: 50%;
        left: 50%;
        width: 800px;
        height: 400px;
        transform: translate(-50%, -50%);
        background-color: white;
        padding: 20px;
        border-radius: 5px;
        z-index: 10000;
    }

    .modal-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.5);
        z-index: 9999;
    }

    .close {
        position: absolute;
        top: 10px;
        right: 10px;
        font-size: 24px;
        font-weight: bold;
        cursor: pointer;
    }


</style>






