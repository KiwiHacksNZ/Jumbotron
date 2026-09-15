<script>
    import { base } from '$app/paths';
    import BrandMark from '$lib/BrandMark.svelte';
    import "$lib/styles.css";
    let { children } = $props();
</script>

<svelte:head>
    <link rel="icon" href={`${base}/brand/kiwi-mascot.png`} />
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,500,0,0" />
</svelte:head>

<div class="site-shell">
    <main>{@render children()}</main>
    <footer>
        <a href="https://www.kiwihacks.org" aria-label="Visit KiwiHacks"><BrandMark compact /></a>
        <p>Built for KiwiHackers across Aotearoa.</p>
        <nav aria-label="Footer links">
            <a href="https://www.kiwihacks.org">KiwiHacks.org</a>
            <a href="mailto:niko@kiwihacks.org">Get help</a>
            <a href="https://github.com/hackclub/jumbotron">Original project</a>
        </nav>
    </footer>
</div>

<style>
    .site-shell { min-height:100vh; display:flex; flex-direction:column; }
    main { flex:1; }
    footer { display:flex; flex-wrap:wrap; align-items:center; justify-content:center; gap:2rem; padding:2rem; color:var(--very-dark-green); border-top:1.5px dashed var(--very-dark-green); text-align:center; }
    footer :global(.brand) { text-decoration:none; }
    footer p { margin:0; font-size:1.05rem; }
    nav { display:flex; gap:1.2rem; }
    nav a { color:inherit; text-decoration:underline; transition:color .3s ease; }
    nav a:hover { color:var(--bright-blue); }
    @media (max-width:720px) { footer { gap:1rem; } nav { flex-wrap:wrap; gap:1rem; } }
</style>
