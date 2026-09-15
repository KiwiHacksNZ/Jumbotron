<script>
    import { base } from "$app/paths";
    import { onMount } from "svelte";
    import BrandMark from "$lib/BrandMark.svelte";
    import { eventName } from "$lib/event.js";
    import MainConfig from "$lib/configs/main.svelte";
    import AnnouncementConfig from "$lib/configs/announcements.svelte";
    import Slides from "$lib/configs/slides.svelte";
    import { sync } from "$lib/sync.svelte.js";

    let clock = $state("");
    let syncing = $derived(sync.enabled || sync.announcements || sync.slides);
    onMount(() => {
        const tick = () => clock = new Intl.DateTimeFormat("en-NZ", { hour:"2-digit", minute:"2-digit", second:"2-digit", hour12:false }).format(new Date());
        tick(); const timer = setInterval(tick, 1000);
        return () => clearInterval(timer);
    });
</script>

<svelte:head><title>Control room · {eventName}</title></svelte:head>

<nav class="navbar">
    <a href="https://www.kiwihacks.org" aria-label="KiwiHacks"><BrandMark compact /></a>
    <span class="nav-piece">{eventName}</span>
    <span class="nav-piece status" class:busy={syncing}><i></i>{syncing ? "Syncing displays" : "All systems ready"}</span>
    <time class="nav-piece">{clock}</time>
</nav>

<main class="console">
    <img class="stars" src={`${base}/brand/stars.png`} alt="" aria-hidden="true" />
    <img class="stars" src={`${base}/brand/stars.png`} alt="" aria-hidden="true" />
    <img class="stars" src={`${base}/brand/stars.png`} alt="" aria-hidden="true" />

    <h1 class="headline">Run the room.</h1>
    <p class="lede">Keep this tab on your laptop, put the display window on the projector, and send everything from here.</p>

    <section class="card">
        <h2>Display</h2>
        <p class="card-sub">Launch and connect the projector view</p>
        <MainConfig />
    </section>
    <section class="card">
        <h2>Live updates</h2>
        <p class="card-sub">Announcements and what’s coming up</p>
        <AnnouncementConfig title="" />
    </section>
    <section class="card">
        <h2>Present</h2>
        <p class="card-sub">Share a file, a Canva design, a video or a QR code</p>
        <Slides />
    </section>

    <p class="fineprint">Jumbotron syncs tabs in the same browser — keep the console and the display in one window.</p>
</main>

<style>
    .navbar { position:relative; z-index:100; display:flex; flex-wrap:wrap; align-items:center; justify-content:center; gap:2.5rem; padding:1.2rem; margin-bottom:.9rem; background:var(--semi-opaque-green); border:1.5px dashed var(--very-dark-green); }
    .navbar a { text-decoration:none; }
    .nav-piece { font:500 .8rem var(--font-mono); color:var(--very-dark-green); }
    .status { display:flex; align-items:center; gap:.5rem; }
    .status i { width:.55rem; height:.55rem; border-radius:50%; background:var(--medium-green); box-shadow:0 0 0 4px rgba(118,176,112,.28); }
    .status.busy i { background:var(--bright-blue); animation:pulse 1s infinite; }
    @keyframes pulse { 50% { opacity:.35; } }

    .console { position:relative; max-width:820px; margin:auto; padding:2.5rem 1.5rem 5rem; text-align:center; }
    .stars { position:absolute; z-index:-1; max-width:10vw; height:auto; user-select:none; }
    .stars:nth-child(1) { top:0; left:-6%; transform:rotate(-10deg); }
    .stars:nth-child(2) { top:14%; right:-8%; max-width:12vw; transform:rotate(25deg); }
    .stars:nth-child(3) { top:42%; left:-9%; max-width:8vw; transform:rotate(45deg); }

    .headline { max-width:22ch; margin:.5rem auto 0; font-family:var(--font-mono); font-size:3.4rem; }
    .lede { max-width:56ch; margin:1rem auto 2.5rem; font-size:1.35rem; }

    .card { margin-bottom:1.5rem; padding:1.75rem; background:#fff; border:1.5px dashed var(--dark-green); border-radius:15px; }
    .card h2 { margin:0; font-size:2rem; }
    .card-sub { margin:.35rem 0 1.25rem; color:var(--dark-green); }
    .card :global(p) { margin-left:auto; margin-right:auto; text-align:center; }
    .card :global(table) { margin:auto; }
    .card :global(h4) { font-size:1.4rem; }
    .fineprint { font:500 .9rem var(--font-mono); color:var(--dark-green); }

    @media screen and (max-width:768px) {
        .stars { display:none; }
        .navbar { gap:1rem; }
        .headline { font-size:2.2rem; }
        .lede { font-size:1.15rem; }
    }
</style>
