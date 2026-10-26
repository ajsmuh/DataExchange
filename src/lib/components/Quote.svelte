<script>
    let { children, author = "Not known" } = $props();
    let quoteElement;
    let toast = $state("");

    function showToast(message) {
        toast = message;
        setTimeout(() => (toast = ""), 2500);
    }

    async function notify(message) {
        // Fall back to an in-page toast if notifications aren't possible
        if (!("Notification" in window)) {
            showToast(message);
            return;
        }

        let permission = Notification.permission;
        if (permission === "default") {
            permission = await Notification.requestPermission();
        }

        if (permission !== "granted") {
            showToast(message);
            return;
        }

        try {
            // Works on mobile Chrome when a service worker is registered
            const reg = await navigator.serviceWorker?.getRegistration();
            if (reg) {
                await reg.showNotification(message);
            } else {
                new Notification(message);
            }
        } catch {
            showToast(message);
        }
    }

    async function copyQuote() {
        const text = quoteElement.innerText.trim();

        try {
            if (navigator.clipboard && window.isSecureContext) {
                await navigator.clipboard.writeText(text);
            } else {
                // Fallback for non-HTTPS contexts
                const ta = document.createElement("textarea");
                ta.value = text;
                ta.style.position = "fixed";
                ta.style.opacity = "0";
                document.body.appendChild(ta);
                ta.select();
                document.execCommand("copy");
                document.body.removeChild(ta);
            }
        } catch {
            showToast("Couldn't copy the quote");
            return;
        }

        await notify("You successfully copied the quote to your clipboard!");
    }

    async function share() {
        if (!navigator.share) {
            showToast("Sharing isn't supported on this device");
            return;
        }
        try {
            await navigator.share({
                title: "Quote",
                text: quoteElement.innerText.trim(),
            });
        } catch {
            // user cancelled the share sheet; ignore
        }
    }
</script>

<div class="space-y-3 rounded-lg border border-gray-200 p-4">
    <header class="font-semibold text-gray-700">
        {author} says:
    </header>

    <blockquote
        class="rounded-md border-l-4 border-gray-400 bg-gray-50 p-4 italic text-gray-700"
        bind:this={quoteElement}
    >
        {@render children?.()}
    </blockquote>

    <div class="flex gap-2">
        <button
            onclick={copyQuote}
            class="rounded-md border border-gray-300 px-4 py-2 hover:bg-gray-100"
        >
            Copy
        </button>

        <button
            onclick={share}
            class="rounded-md bg-pink-400 px-4 py-2 text-white hover:bg-pink-500"
        >
            Share
        </button>
    </div>

    {#if toast}
        <p class="rounded-md bg-gray-800 px-3 py-2 text-sm text-white" role="status">
            {toast}
        </p>
    {/if}
</div>