
<script>
    import Quote from "#lib/components/Quote.svelte";

    // Ort, an dem das Angebot erscheinen soll
    const ZIEL_LAT = 42.05910523134263;
    const ZIEL_LNG = 19.517268301553923;
    const RADIUS = 15; // in Metern

    let abstand = $state(null);
    let imBereich = $derived(abstand !== null && abstand <= RADIUS);

    // Abstand zwischen zwei Koordinaten in Metern
    function berechneAbstand(lat1, lng1, lat2, lng2) {
        const R = 6371000;
        const rad = (grad) => (grad * Math.PI) / 180;
        const dLat = rad(lat2 - lat1);
        const dLng = rad(lng2 - lng1);

        const a =
            Math.sin(dLat / 2) ** 2 +
            Math.cos(rad(lat1)) *
                Math.cos(rad(lat2)) *
                Math.sin(dLng / 2) ** 2;

        return 2 * R * Math.asin(Math.sqrt(a));
    }

    // Position live beobachten
    $effect(() => {
        const id = navigator.geolocation.watchPosition(
            (pos) => {
                abstand = berechneAbstand(
                    pos.coords.latitude,
                    pos.coords.longitude,
                    ZIEL_LAT,
                    ZIEL_LNG
                );
            },
            (fehler) => console.log("Geolocation Fehler:", fehler.message),
            { enableHighAccuracy: true }
        );

        return () => navigator.geolocation.clearWatch(id);
    });
</script>

<main class="min-h-screen bg-[#faf9f6] px-6 py-20">
    <div class="mx-auto max-w-2xl">
        <header class="mb-16">
            <p class="text-xs tracking-[0.25em] text-stone-400 uppercase">
                Zitatsammlung
            </p>

            <h1 class="mt-4 font-['Instrument_Serif'] text-6xl leading-none text-stone-900 sm:text-7xl">
                Data <i class="text-[#c0603a]">Exchange</i>
            </h1>

            <p class="mt-5 max-w-sm text-stone-500">
                Sieben Sätze, die bleiben. Zum Lesen, Kopieren und Teilen.
            </p>

            {#if abstand !== null}
                <p class="mt-3 text-xs text-stone-400">
                    Abstand zum Ort: {Math.round(abstand)} m
                </p>
            {/if}
        </header>

        {#if imBereich}
            <section class="mb-16 rounded-2xl border border-[#c0603a]/30 bg-[#c0603a]/10 p-6 sm:p-8">
                <p class="text-xs tracking-[0.25em] text-[#c0603a] uppercase">
                    Neues Angebot
                </p>

                <h2 class="mt-3 font-['Instrument_Serif'] text-3xl text-stone-900 sm:text-4xl">
                    Du bist vor Ort!
                </h2>

                <p class="mt-3 text-stone-600">
                    Nur hier und nur jetzt: 20 % Rabatt auf alles. Zeig diese Nachricht an der Kasse.
                </p>
            </section>
        {/if}

        <Quote author="Ajsela Muhja">
            Das ist mein Quote.
        </Quote>

        <Quote author="Unknown">
            Another quote
        </Quote>

        <Quote author="Serina Guri">
            You are worth it.
        </Quote>

        <Quote author="Luigj Shala">
            Love yourself.
        </Quote>

        <Quote author="Sara Zaganjori">
            You can do it, believe in yourself.
        </Quote>

        <Quote author="Edona Peraj">
            Choose your dreams.
        </Quote>

        <Quote author="Hermes Zalla">
            Mind your business, always.
        </Quote>

        <footer class="border-t border-stone-200 pt-8 text-center font-['Instrument_Serif'] text-xl text-stone-400 italic">
            Ende.
        </footer>
    </div>
</main>
