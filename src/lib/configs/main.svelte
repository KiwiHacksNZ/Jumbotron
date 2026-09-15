<script>
    import { base } from "$app/paths";
    import { onMount } from "svelte";

    import { sync } from "$lib/sync.svelte.js";
    import { tutorial } from "$lib/sync.svelte.js";

    let displayName = $state("");

    onMount(function() {
        displayName = localStorage.getItem("jumbotron.eventName") || "";
        localStorage.setItem("jumbotron.error", "");
       setTimeout(function() {localStorage.setItem("jumbotron.sync", true);}, 1000);
       setTimeout(function() {localStorage.setItem("jumbotron.sync", false);}, 2000);
    })

    function openDisplay() {
        window.open(base + "/display", "_blank");
        setTimeout(function() {localStorage.setItem("jumbotron.sync", true);}, 2000);
       setTimeout(function() {localStorage.setItem("jumbotron.sync", false);}, 3000);
    }
    function syncDisplay() {
        sync.enabled = true;
        document.getElementById("sync").disabled = true;
        setTimeout(function() {localStorage.setItem("jumbotron.sync", true)}, 2000)
        setTimeout(function() {sync.enabled = false; document.getElementById("sync").disabled = false; localStorage.setItem("jumbotron.sync", false)}, 3000)
    }
    function saveName() {
        localStorage.setItem("jumbotron.eventName", displayName.trim());
        setTimeout(function() {localStorage.setItem("jumbotron.sync", true);}, 200);
        setTimeout(function() {localStorage.setItem("jumbotron.sync", false);}, 700);
    }
    function fullscreen() {
        document.documentElement.requestFullscreen();
    }

</script>
{#if tutorial.enabled}<p>Display windows should be moved to another screen.</p>{/if}
<!--<p><button onclick={openDisplay}>Open New Display Window</button></p>-->
<p><button class="bigButton" title="Open a display window" onclick={openDisplay}><span translate="no" class="material-symbols-outlined">add_to_queue</span></button></p>
{#if tutorial.enabled}<p>New windows are not automatically synced. Sync the windows using the buttons for each section below.</p>{/if}
<form onsubmit={(e) => { e.preventDefault(); saveName(); }}>
    <input bind:value={displayName} placeholder="Nova Auckland" aria-label="Event name shown on the display" />
    <p><button type="submit">Name this event</button></p>
</form>
<p>Drag that window onto the projector, then press <span class="key">f</span> in it for fullscreen.</p>
<p>Press <span class="key">esc</span> to come back out.</p>
