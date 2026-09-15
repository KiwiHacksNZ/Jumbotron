<script>
    import { base } from "$app/paths";
    import { onMount } from "svelte";
    import { blur, fade, fly, slide } from "svelte/transition";
    import { eventName } from "$lib/event.js";

    let event = $state([]);
    let announcement = $state([]);
    let presentation = $state("");
    let presentationType = $state(0);
    let clock = $state("");
    let displayName = $state("");
    let screenY = $state(0);
    let screenX = $state(0);

    const starPositions = [
        { top: "4%",  left: "3%",   width: "13vw", rotate: "-12deg" },
        { top: "2%",  left: "34%",  width: "8vw",  rotate: "18deg" },
        { top: "6%",  right: "4%",  width: "14vw", rotate: "35deg" },
        { top: "38%", left: "1%",   width: "10vw", rotate: "60deg" },
        { top: "34%", right: "1%",  width: "9vw",  rotate: "-25deg" },
        { bottom: "4%", left: "5%", width: "12vw", rotate: "22deg" },
        { bottom: "2%", right: "6%", width: "13vw", rotate: "-40deg" },
        { bottom: "26%", right: "22%", width: "6vw", rotate: "8deg" }
    ];

    function sync() {
        displayName = localStorage.getItem("jumbotron.eventName") || eventName;
        const eventTitle = localStorage.getItem("jumbotron.event.title") || "";
        const eventLabel = localStorage.getItem("jumbotron.event.label") || "";
        event = eventTitle && eventLabel ? [eventTitle, eventLabel] : [];
        const title = localStorage.getItem("jumbotron.announcement.title") || "";
        const message = localStorage.getItem("jumbotron.announcement.message") || "";
        announcement = title || message ? [title, message] : [];
        const youtube = localStorage.getItem("jumbotron.ytLink") || "";
        const file = localStorage.getItem("jumbotron.fileLink") || "";
        const qr = localStorage.getItem("jumbotron.qrLink") || "";
        const canva = localStorage.getItem("jumbotron.canvaLink") || "";
        if (youtube) { presentation = youtube; presentationType = 1; }
        else if (file) { presentation = file; presentationType = 2; }
        else if (qr) { presentation = qr; presentationType = 3; }
        else if (canva) { presentation = canva; presentationType = 4; }
        else { presentation = ""; presentationType = 0; }
    }

    onMount(() => {
        sync();
        const tick = () => clock = new Intl.DateTimeFormat("en-NZ", { hour:"2-digit", minute:"2-digit", hour12:false }).format(new Date());
        tick();
        const timer = setInterval(tick, 10000);
        const storage = () => sync();
        const fullscreen = (e) => { if (e.key.toLowerCase() === "f") document.documentElement.requestFullscreen?.(); };
        window.addEventListener("storage", storage);
        window.addEventListener("keydown", fullscreen);
        return () => { clearInterval(timer); window.removeEventListener("storage", storage); window.removeEventListener("keydown", fullscreen); };
    });
</script>

<svelte:window bind:innerHeight={screenY} bind:innerWidth={screenX} />
<svelte:head><title>{displayName}</title></svelte:head>

<div class="display">
    {#each starPositions as star}
        <img class="star" src={`${base}/brand/stars.png`} alt="" aria-hidden="true"
             style="top:{star.top ?? 'auto'}; bottom:{star.bottom ?? 'auto'}; left:{star.left ?? 'auto'}; right:{star.right ?? 'auto'}; width:{star.width}; transform:rotate({star.rotate});" />
    {/each}

    <div class="stage" class:hidden={announcement.length > 0}>
        <img class="nova-logo" src={`${base}/brand/nova-logo.png`} alt="Nova by KiwiHacks" />
        <p class="event-name">{displayName}</p>
    </div>

    <div class="ground">
        {#if event.length}
            <span class="up-next" class:now={event[1] === "Now"} transition:slide>
                <strong>{event[0]}</strong>
                <span class="when">{event[1]}</span>
            </span>
        {/if}
        <time>{clock}</time>
    </div>
    <img class="mascot" class:tucked={announcement.length > 0} src={`${base}/brand/kiwi-mascot.png`} alt="" aria-hidden="true" />

    {#if announcement.length}
        <div class="announcement" in:fly={{ y: 400, duration: 900 }} out:fly={{ y: -400, duration: 700 }}>
            {#if announcement[0]}<h1>{announcement[0]}</h1>{/if}
            {#if announcement[1]}<p>{announcement[1]}</p>{/if}
        </div>
    {/if}

    {#if presentation}
        <div class="presentation" transition:blur>
            {#if presentationType === 3}
                <div class="qr-card" in:fade>
                    <h2>Scan this code</h2>
                    <img src={`https://api.qrserver.com/v1/create-qr-code/?data=${encodeURIComponent(presentation)}&size=500x500&bgcolor=ffffff&color=132f1e`} alt="QR code" />
                </div>
            {:else}
                <iframe src={presentation} width={screenX} height={screenY} title={presentationType === 1 ? "YouTube video" : presentationType === 4 ? "Canva design" : "Shared presentation"} allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
            {/if}
        </div>
    {/if}

</div>

<style>
    /* Nova sub-brand (nova.kiwihacks.org): navy field, pink band, Jua. Display only. */
    :global(footer) { display:none!important; }
    .display { position:fixed; inset:0; z-index:1000; overflow:hidden; background:linear-gradient(180deg, var(--nova-bg), var(--nova-bg-bottom)); color:#fff; font-family:var(--font-nova); }

    .star { position:absolute; height:auto; opacity:.16; filter:brightness(0) invert(1); user-select:none; }

    .ground { position:absolute; z-index:2; left:0; right:0; bottom:0; height:22vh; display:flex; align-items:center; justify-content:center; gap:2.5rem; padding:0 3vw; background:var(--nova-pink); color:#fff; font-family:var(--font-nova); }
    .ground time { font-size:clamp(3rem,9vw,7rem); line-height:1; }
    .up-next { margin-right:auto; padding-left:clamp(8rem,28vw,24rem); display:flex; align-items:baseline; gap:1.25rem; min-width:0; }
    .up-next strong { font-weight:400; font-size:clamp(1.6rem,3.4vw,3rem); white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
    .up-next .when { font-size:clamp(1.3rem,2.4vw,2rem); white-space:nowrap; }
    .up-next.now strong, .up-next.now .when { animation:pulse 1.4s infinite; }

    .stage { position:absolute; inset:0 0 26vh; display:flex; flex-direction:column; align-items:center; justify-content:center; text-align:center; transition:opacity .6s ease; }
    .stage.hidden { opacity:0; }
    .nova-logo { width:min(46vw,700px); max-height:38vh; object-fit:contain; }
    .event-name { margin:1.25rem 0 0; font-family:var(--font-nova); font-size:clamp(2rem,5vw,4.5rem); line-height:1; }

    .mascot { position:absolute; z-index:6; left:4vw; bottom:17vh; width:min(22vw,320px); height:auto; transition:bottom .6s ease; user-select:none; }
    .mascot.tucked { bottom:-30vh; }


    .announcement { position:absolute; z-index:4; inset:14vh 6vw auto; display:flex; flex-direction:column; align-items:center; text-align:center; }
    .announcement h1 { margin:0; font-size:clamp(3.5rem,9vw,9rem); line-height:1; }
    .announcement p { max-width:34ch; margin:2rem 0 0; font-size:clamp(1.5rem,3vw,3rem); line-height:1.3; }

    .presentation { position:absolute; inset:0; z-index:7; background:var(--nova-bg); }
    iframe { display:block; border:0; }
    .qr-card { height:100%; display:flex; flex-direction:column; align-items:center; justify-content:center; }
    .qr-card h2 { margin:0 0 2rem; font-size:clamp(2rem,4.5vw,4rem); }
    .qr-card img { width:min(48vh,440px); padding:1rem; background:#fff; border:1.5px dashed #fff; border-radius:15px; }

    @keyframes pulse { 50% { opacity:.75; } }
    @media screen and (max-width:768px) {
        .star { display:none; }
        .mascot { width:36vw; bottom:18vh; }
        .ground { gap:1rem; padding:0 1rem; }
        .up-next { padding-left:42vw; }
    }
</style>
