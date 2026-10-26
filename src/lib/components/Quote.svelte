<script>
    let { children, author="Not known" } = $props();
    let quoteElement;
 
    async function copyQuote() {
        await navigator.clipboard.writeText(
            quoteElement.innerText.trim()
        );
        let permission = Notification.permission;
        if (permission === "granted") {
            permission = await Notification.requestPermission();
        }
 
            if (permission === "granted") {
                new Notification("You successfully copied the quote to your clipboard!");
        }
    }

    async function share() {
        await navigator.share({
            title: "Quote",
            text: quoteElement.innerText.trim(),
        });
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
</div>