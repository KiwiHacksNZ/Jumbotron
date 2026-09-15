<script>
    import { onMount } from "svelte";
    import { slide } from "svelte/transition";

    import { sync, tutorial } from "$lib/sync.svelte.js";
    import { liveshareData, updateAPI } from "$lib/liveshare.svelte.js";

    const keys = ["jumbotron.fileLink", "jumbotron.ytLink", "jumbotron.qrLink", "jumbotron.canvaLink"];

    let consoleMode = $state(0);
    let fileLink = $state("");
    let ytLink = $state("");
    let qrLink = $state("");
    let canvaLink = $state("");
    let canvaError = $state("");
    let mountedEnabled = $state(false);

    onMount(() => keys.forEach((key) => localStorage.setItem(key, "")));

    function mount(key, embed, shared) {
        sync.slides = true;
        keys.forEach((item) => localStorage.setItem(item, item === key ? embed : ""));
        liveshareData.presentation = shared;
        updateAPI();
        setTimeout(() => { localStorage.setItem("jumbotron.sync", true); sync.liveshare = true; mountedEnabled = Boolean(embed); }, 2000);
        setTimeout(() => { localStorage.setItem("jumbotron.sync", false); sync.slides = false; sync.liveshare = false; }, 3000);
    }

    function iframeSrc(raw) {
        return raw.indexOf("<iframe") != -1 ? raw.split('"')[1] : null;
    }

    function enableFile() {
        const link = iframeSrc(fileLink) || `https://drive.google.com/file/d/${fileLink.split("/")[5]}/preview`;
        mount("jumbotron.fileLink", link, link);
    }

    function enableYoutube() {
        mount("jumbotron.ytLink", `https://www.youtube.com/embed/${getYTID(ytLink)}`, ytLink);
    }

    function enableQR() {
        mount("jumbotron.qrLink", qrLink, qrLink);
    }

    function enableCanva() {
        const embed = getCanvaEmbed(canvaLink);
        if (!embed) {
            canvaError = "That looks like a share link. In Canva use Share → More → Embed, then paste the embed code or the link it gives you.";
            return;
        }
        canvaError = "";
        mount("jumbotron.canvaLink", embed, canvaLink);
    }

    function unmountDisplay() {
        mountedEnabled = false;
        mount(null, "", null);
    }

    // Canva only renders in an iframe from its embed URL; a plain share/view link is refused, so we insist on the embed one.
    function getCanvaEmbed(url) {
        const src = iframeSrc(url);
        if (src) return src.includes("?embed") ? src : null;
        const clean = url.trim();
        return clean.includes("?embed") ? clean : null;
    }

    // This is an AI generated function; I still don't understand why my function didn't work while this does... but if it works it works
    function getYTID(url) {
        const regExp = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|&v=)([^#&?]*).*/;
        const match = url.match(regExp);
        return (match && match[2].length === 11) ? match[2] : null;
    }
</script>

<style>
    form input { margin:.5rem auto; }
    h4 { margin-top:0; font-size:1.2rem; }
    button.disabled { cursor:progress; }
    .error { margin:.6rem auto 0; max-width:640px; padding:.7rem .9rem; border:1.5px dashed var(--dark-green); border-radius:15px; background:var(--very-light-blue); font-size:.9rem; }
</style>

{#if !mountedEnabled}
<div transition:slide>
    <p>
        <button class="bigButton" class:toggleOn={consoleMode == 1} title="Display Google Drive file" onclick={() => consoleMode = consoleMode == 1 ? 0 : 1}><span class="material-symbols-outlined" translate="no">drive_export</span></button>
        <button class="bigButton" class:toggleOn={consoleMode == 4} title="Display Canva design" onclick={() => consoleMode = consoleMode == 4 ? 0 : 4}><span class="material-symbols-outlined" translate="no">palette</span></button>
        <button class="bigButton" class:toggleOn={consoleMode == 2} title="Display YouTube video" onclick={() => consoleMode = consoleMode == 2 ? 0 : 2}><span class="material-symbols-outlined" translate="no">video_library</span></button>
        <button class="bigButton" class:toggleOn={consoleMode == 3} title="Configure QR code" onclick={() => consoleMode = consoleMode == 3 ? 0 : 3}><span class="material-symbols-outlined" translate="no">qr_code</span></button>
    </p>

    {#if consoleMode == 1}
        <div class="subconsole">
            <h4>Google Drive file</h4>
            <p>Share the file in Drive, <span class="key">copy</span> the link and <span class="key">paste</span> it below. Make sure anyone with the link can view it.</p>
            <p>For Google Slides, use <i>Publish to web</i> → <i>Embed</i> and paste that link (or the whole embed code).</p>
            <form>
                <input bind:value={fileLink} class="bigInput" type="url" placeholder="https://drive.google.com/file...">
            </form>
            {#if tutorial.enabled}<p>Each display window scrolls the document on its own—they will not stay in step with each other.</p>{/if}
            <p><button onclick={enableFile} id="file" class:disabled={sync.slides} class:incomplete={fileLink.length == 0} disabled={sync.slides || fileLink.length == 0}>Put it on the big screen</button></p>
        </div>

    {:else if consoleMode == 4}
        <div class="subconsole">
            <h4>Canva design</h4>
            <p>In Canva, hit <i>Share</i> → <i>More</i> → <i>Embed</i>, then <span class="key">copy</span> the embed code or the link beside it. An ordinary share link will not play.</p>
            <form>
                <input bind:value={canvaLink} oninput={() => canvaError = ""} class="bigInput" placeholder="https://www.canva.com/design/.../watch?embed">
            </form>
            {#if canvaError}<p class="error" role="alert">{canvaError}</p>{/if}
            {#if tutorial.enabled}<p>Canva embeds play in the display window—use the arrows on the design to move through slides. Each display window advances on its own.</p>{/if}
            <p><button onclick={enableCanva} id="canva" class:disabled={sync.slides} class:incomplete={canvaLink.length == 0} disabled={sync.slides || canvaLink.length == 0}>Put it on the big screen</button></p>
        </div>

    {:else if consoleMode == 2}
        <div class="subconsole">
            <h4>YouTube video</h4>
            <p><span class="key">Copy</span> the video link straight from YouTube and <span class="key">paste</span> it below.</p>
            <form>
                <input bind:value={ytLink} class="bigInput" type="url" placeholder="https://www.youtube.com/watch...">
            </form>
            {#if tutorial.enabled}<p>Each display window plays the video on its own—they will not stay in sync.</p>{/if}
            <p><button onclick={enableYoutube} id="youtube" class:disabled={sync.slides} class:incomplete={ytLink.length == 0} disabled={sync.slides || ytLink.length == 0}>Put it on the big screen</button></p>
        </div>

    {:else if consoleMode == 3}
        <div class="subconsole">
            <h4>QR code</h4>
            <p><span class="key">Copy</span> and <span class="key">paste</span> the link you want everyone to scan.</p>
            <form>
                <input bind:value={qrLink} class="bigInput" type="url" placeholder="https://www.kiwihacks.org...">
            </form>
            <p><button onclick={enableQR} id="qrcode" class:disabled={sync.slides} class:incomplete={qrLink.length == 0} disabled={sync.slides || qrLink.length == 0}>Put it on the big screen</button></p>
        </div>
    {/if}
</div>
{:else}
<div transition:slide>
    <p>That’s live on your display windows now. Take it down before mounting something else.</p>
    <p><button class="secondary" onclick={unmountDisplay}>Clear the big screen</button></p>
</div>
{/if}
