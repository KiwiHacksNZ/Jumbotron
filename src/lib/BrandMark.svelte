<script>
    import { base } from "$app/paths";
    let { compact = false } = $props();
</script>

<span class:compact class="brand" aria-label="KiwiHacks Jumbotron">
    <img src={`${base}/brand/kiwihacks-wordmark.png`} alt="KiwiHacks" />
    {#if !compact}<span>JUMBOTRON</span>{/if}
</span>

<style>
    .brand { display:inline-flex; flex-direction:column; align-items:flex-start; color:var(--very-dark-green); }
    img { display:block; width:150px; height:auto; object-fit:contain; }
    span span { margin-top:.25rem; padding-left:.15rem; font:600 .56rem/1 var(--font-mono); letter-spacing:.22em; }
    .compact img { width:128px; }
</style>
